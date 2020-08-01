---
title: Configuração no ASP.NET Core
author: rick-anderson
description: Saiba como usar a API de configuração para configurar um aplicativo do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/index
ms.openlocfilehash: a08993a7909d67be34446815b10d32089d9e0629
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444155"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="2fd71-103">Configuração no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fd71-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="2fd71-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="2fd71-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2fd71-105">A configuração no ASP.NET Core é executada usando um ou mais [provedores de configuração](#cp).</span><span class="sxs-lookup"><span data-stu-id="2fd71-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="2fd71-106">Os provedores de configuração lêem dados de configuração de pares chave-valor usando uma variedade de fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="2fd71-107">Arquivos de configurações, como *appsettings.jsem*</span><span class="sxs-lookup"><span data-stu-id="2fd71-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="2fd71-108">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="2fd71-108">Environment variables</span></span>
* <span data-ttu-id="2fd71-109">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="2fd71-109">Azure Key Vault</span></span>
* <span data-ttu-id="2fd71-110">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="2fd71-110">Azure App Configuration</span></span>
* <span data-ttu-id="2fd71-111">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="2fd71-111">Command-line arguments</span></span>
* <span data-ttu-id="2fd71-112">Provedores personalizados, instalados ou criados</span><span class="sxs-lookup"><span data-stu-id="2fd71-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="2fd71-113">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="2fd71-113">Directory files</span></span>
* <span data-ttu-id="2fd71-114">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="2fd71-114">In-memory .NET objects</span></span>

<span data-ttu-id="2fd71-115">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2fd71-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="2fd71-116">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="2fd71-116">Default configuration</span></span>

<span data-ttu-id="2fd71-117">ASP.NET Core aplicativos Web criados com [dotnet novo](/dotnet/core/tools/dotnet-new) ou o Visual Studio geram o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fd71-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="2fd71-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="2fd71-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="2fd71-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) : Adiciona um existente `IConfiguration` como uma origem.</span><span class="sxs-lookup"><span data-stu-id="2fd71-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="2fd71-120">No caso de configuração padrão, o adiciona a configuração do [host](#hvac) e a define como a primeira origem para a configuração do _aplicativo_ .</span><span class="sxs-lookup"><span data-stu-id="2fd71-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="2fd71-121">[appsettings.js](#appsettingsjson) usando o [provedor de configuração JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2fd71-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="2fd71-122">*appSettings.* `Environment` *. JSON* usando o [provedor de configuração JSON](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2fd71-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="2fd71-123">Por exemplo, *appSettings*. ***Produção***. *JSON* e *appSettings*. ***Desenvolvimento***. *JSON*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="2fd71-124">[Segredos do aplicativo](xref:security/app-secrets) quando o aplicativo é executado no `Development` ambiente.</span><span class="sxs-lookup"><span data-stu-id="2fd71-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="2fd71-125">Variáveis de ambiente usando o [provedor de configuração de variáveis de ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="2fd71-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="2fd71-126">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line).</span><span class="sxs-lookup"><span data-stu-id="2fd71-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="2fd71-127">Os provedores de configuração adicionados posteriormente substituem as configurações de chave anteriores.</span><span class="sxs-lookup"><span data-stu-id="2fd71-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="2fd71-128">Por exemplo, se `MyKey` for definido tanto no *appsettings.jsquanto no* ambiente, o valor do ambiente será usado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="2fd71-129">Usando os provedores de configuração padrão, o [provedor de configuração de linha de comando](#clcp) substitui todos os outros provedores.</span><span class="sxs-lookup"><span data-stu-id="2fd71-129">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="2fd71-130">Para obter mais informações sobre o `CreateDefaultBuilder` , consulte [configurações padrão do Construtor](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="2fd71-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="2fd71-131">O código a seguir exibe os provedores de configuração habilitados na ordem em que foram adicionados:</span><span class="sxs-lookup"><span data-stu-id="2fd71-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="2fd71-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="2fd71-132">appsettings.json</span></span>

<span data-ttu-id="2fd71-133">Considere o seguinte *appsettings.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="2fd71-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="2fd71-134">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="2fd71-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2fd71-135">O padrão <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="2fd71-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="2fd71-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="2fd71-136">*appsettings.json*</span></span>
1. <span data-ttu-id="2fd71-137">*appSettings.* `Environment` *. JSON* : por exemplo, *appSettings*. ***Produção***. *JSON* e *appSettings*. ***Desenvolvimento***. arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="2fd71-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="2fd71-138">A versão de ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="2fd71-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="2fd71-139">Para obter mais informações, consulte <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="2fd71-140">*appSettings*. `Environment` . os valores *JSON* substituem as chaves no *appsettings.jsem*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="2fd71-141">Por exemplo, por padrão:</span><span class="sxs-lookup"><span data-stu-id="2fd71-141">For example, by default:</span></span>

* <span data-ttu-id="2fd71-142">Em desenvolvimento, *appSettings*. ***Desenvolvimento***. a configuração *JSON* substitui os valores encontrados no *appsettings.jsem*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="2fd71-143">Em produção, *appSettings*. ***Produção***. a configuração *JSON* substitui os valores encontrados no *appsettings.jsem*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="2fd71-144">Por exemplo, ao implantar o aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="2fd71-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="2fd71-145">Associar dados de configuração hierárquica usando o padrão de opções</span><span class="sxs-lookup"><span data-stu-id="2fd71-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="2fd71-146">Usando a configuração [padrão](#default) , o *appsettings.jsem* e *appSettings.* `Environment` os arquivos *. JSON* são habilitados com [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span><span class="sxs-lookup"><span data-stu-id="2fd71-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="2fd71-147">As alterações feitas no *appsettings.jsem* e *appSettings.* `Environment` o arquivo *. JSON* ***após*** o início do aplicativo é lido pelo [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2fd71-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="2fd71-148">Consulte [provedor de configuração JSON](#jcp) neste documento para obter informações sobre como adicionar arquivos de configuração JSON adicionais.</span><span class="sxs-lookup"><span data-stu-id="2fd71-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="2fd71-149">Gerenciador de segurança e segredo</span><span class="sxs-lookup"><span data-stu-id="2fd71-149">Security and secret manager</span></span>

<span data-ttu-id="2fd71-150">Diretrizes de dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-150">Configuration data guidelines:</span></span>

* <span data-ttu-id="2fd71-151">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="2fd71-151">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="2fd71-152">O [Gerenciador de segredo](xref:security/app-secrets) pode ser usado para armazenar segredos no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2fd71-152">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="2fd71-153">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2fd71-153">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="2fd71-154">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="2fd71-154">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="2fd71-155">Por [padrão](#default), o [Gerenciador de segredo](xref:security/app-secrets) lê as definições de configuração depois *deappsettings.js* e *appSettings.* `Environment` *. JSON*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-155">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="2fd71-156">Para obter mais informações sobre como armazenar senhas ou outros dados confidenciais:</span><span class="sxs-lookup"><span data-stu-id="2fd71-156">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="2fd71-157"><xref:security/app-secrets>: Inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="2fd71-157"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="2fd71-158">O Gerenciador de segredo usa o [provedor de configuração de arquivo](#fcp) para armazenar segredos de usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="2fd71-158">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="2fd71-159">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2fd71-159">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="2fd71-160">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-160">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="2fd71-161">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="2fd71-161">Environment variables</span></span>

<span data-ttu-id="2fd71-162">Usando a configuração [padrão](#default) , o <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração de pares chave-valor da variável de ambiente depois de ler *appsettings.jsem*, *appSettings.* `Environment` *. JSON*e o [Gerenciador de segredo](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="2fd71-162">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="2fd71-163">Portanto, os valores de chave lidos dos valores de substituição de ambiente lidos de *appsettings.jsem*, *appSettings.* `Environment` *. JSON*e o Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-163">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="2fd71-164">Os seguintes `set` comandos:</span><span class="sxs-lookup"><span data-stu-id="2fd71-164">The following `set` commands:</span></span>

* <span data-ttu-id="2fd71-165">Defina as chaves de ambiente e os valores do [exemplo anterior](#appsettingsjson) no Windows.</span><span class="sxs-lookup"><span data-stu-id="2fd71-165">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="2fd71-166">Teste as configurações ao usar o [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="2fd71-166">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="2fd71-167">O `dotnet run` comando deve ser executado no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="2fd71-167">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="2fd71-168">As configurações de ambiente anteriores:</span><span class="sxs-lookup"><span data-stu-id="2fd71-168">The preceding environment settings:</span></span>

* <span data-ttu-id="2fd71-169">São definidos apenas em processos iniciados na janela de comando em que foram definidos.</span><span class="sxs-lookup"><span data-stu-id="2fd71-169">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="2fd71-170">Não serão lidos por navegadores iniciados com o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2fd71-170">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="2fd71-171">Os comandos [setx](/windows-server/administration/windows-commands/setx) a seguir podem ser usados para definir as chaves de ambiente e os valores no Windows.</span><span class="sxs-lookup"><span data-stu-id="2fd71-171">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="2fd71-172">Ao contrário `set` de, `setx` as configurações são persistidas.</span><span class="sxs-lookup"><span data-stu-id="2fd71-172">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="2fd71-173">`/M`define a variável no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="2fd71-173">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="2fd71-174">Se a `/M` opção não for usada, uma variável de ambiente do usuário será definida.</span><span class="sxs-lookup"><span data-stu-id="2fd71-174">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="2fd71-175">Para testar se os comandos anteriores substituem *appsettings.js* e *appSettings.* `Environment` *. JSON*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-175">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="2fd71-176">Com o Visual Studio: saia e reinicie o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2fd71-176">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="2fd71-177">Com a CLI: iniciar uma nova janela de comando e Enter `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="2fd71-177">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="2fd71-178">Chame <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> com uma cadeia de caracteres para especificar um prefixo para variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="2fd71-178">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="2fd71-179">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="2fd71-179">In the preceding code:</span></span>

* <span data-ttu-id="2fd71-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`é adicionado após os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="2fd71-180">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="2fd71-181">Para obter um exemplo de como ordenar os provedores de configuração, consulte [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2fd71-181">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="2fd71-182">Variáveis de ambiente definidas com o `MyCustomPrefix_` prefixo substituem os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="2fd71-182">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="2fd71-183">Isso inclui variáveis de ambiente sem o prefixo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-183">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="2fd71-184">O prefixo é eliminado quando os pares chave-valor de configuração são lidos.</span><span class="sxs-lookup"><span data-stu-id="2fd71-184">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="2fd71-185">Os comandos a seguir testam o prefixo personalizado:</span><span class="sxs-lookup"><span data-stu-id="2fd71-185">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="2fd71-186">A [configuração padrão](#default) carrega variáveis de ambiente e argumentos de linha de comando prefixados com `DOTNET_` and `ASPNETCORE_` .</span><span class="sxs-lookup"><span data-stu-id="2fd71-186">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="2fd71-187">Os `DOTNET_` `ASPNETCORE_` prefixos e são usados por ASP.NET Core para [configuração de host e aplicativo](xref:fundamentals/host/generic-host#host-configuration), mas não para a configuração do usuário.</span><span class="sxs-lookup"><span data-stu-id="2fd71-187">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="2fd71-188">Para obter mais informações sobre a configuração de host e aplicativo, consulte [host genérico .net](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="2fd71-188">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="2fd71-189">Em [Azure app serviço](https://azure.microsoft.com/services/app-service/), selecione **nova configuração de aplicativo** na página **configurações > configuração** .</span><span class="sxs-lookup"><span data-stu-id="2fd71-189">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="2fd71-190">Azure App configurações do aplicativo de serviço são:</span><span class="sxs-lookup"><span data-stu-id="2fd71-190">Azure App Service application settings are:</span></span>

* <span data-ttu-id="2fd71-191">Criptografado em repouso e transmitido por um canal criptografado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-191">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="2fd71-192">Exposto como variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2fd71-192">Exposed as environment variables.</span></span>

<span data-ttu-id="2fd71-193">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="2fd71-193">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="2fd71-194">Consulte [prefixos de cadeia de conexão](#constr) para obter informações sobre cadeias de conexão do banco de dados</span><span class="sxs-lookup"><span data-stu-id="2fd71-194">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="2fd71-195">Variáveis de ambiente definidas no launchSettings.jsem</span><span class="sxs-lookup"><span data-stu-id="2fd71-195">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="2fd71-196">As variáveis de ambiente definidas no *launchSettings.js* substituir aquelas definidas no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="2fd71-196">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="2fd71-197">Linha de comando</span><span class="sxs-lookup"><span data-stu-id="2fd71-197">Command-line</span></span>

<span data-ttu-id="2fd71-198">Usando a configuração [padrão](#default) , o <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração de pares de chave-valor de argumento de linha de comando após as seguintes fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-198">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="2fd71-199">*appsettings.js* e *appSettings*. `Environment` . arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="2fd71-199">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="2fd71-200">[Segredos do aplicativo (Gerenciador de segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2fd71-200">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2fd71-201">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2fd71-201">Environment variables.</span></span>

<span data-ttu-id="2fd71-202">Por [padrão](#default), os valores de configuração definidos nos valores de configuração de substituição de linha de comando são definidos com todos os outros provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-202">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="2fd71-203">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="2fd71-203">Command-line arguments</span></span>

<span data-ttu-id="2fd71-204">O comando a seguir define chaves e valores usando `=` :</span><span class="sxs-lookup"><span data-stu-id="2fd71-204">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="2fd71-205">O comando a seguir define chaves e valores usando `/` :</span><span class="sxs-lookup"><span data-stu-id="2fd71-205">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="2fd71-206">O comando a seguir define chaves e valores usando `--` :</span><span class="sxs-lookup"><span data-stu-id="2fd71-206">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="2fd71-207">O valor da chave:</span><span class="sxs-lookup"><span data-stu-id="2fd71-207">The key value:</span></span>

* <span data-ttu-id="2fd71-208">Deve seguir `=` , ou a chave deve ter um prefixo `--` ou `/` quando o valor segue um espaço.</span><span class="sxs-lookup"><span data-stu-id="2fd71-208">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="2fd71-209">Não será necessário se `=` for usado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-209">Isn't required if `=` is used.</span></span> <span data-ttu-id="2fd71-210">Por exemplo, `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-210">For example, `MySetting=`.</span></span>

<span data-ttu-id="2fd71-211">No mesmo comando, não misture pares de chave-valor de argumento de linha de comando que usam `=` com pares de chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="2fd71-211">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="2fd71-212">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="2fd71-212">Switch mappings</span></span>

<span data-ttu-id="2fd71-213">Os mapeamentos de switch permitem a lógica de substituição de nome de **chave** .</span><span class="sxs-lookup"><span data-stu-id="2fd71-213">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="2fd71-214">Forneça um dicionário de substituições de switch para o <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> método.</span><span class="sxs-lookup"><span data-stu-id="2fd71-214">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="2fd71-215">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="2fd71-215">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="2fd71-216">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-216">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="2fd71-217">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="2fd71-217">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="2fd71-218">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="2fd71-218">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="2fd71-219">Os comutadores devem começar com `-` ou `--` .</span><span class="sxs-lookup"><span data-stu-id="2fd71-219">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="2fd71-220">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="2fd71-220">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="2fd71-221">Para usar um dicionário de mapeamentos de opção, passe-o para a chamada para `AddCommandLine` :</span><span class="sxs-lookup"><span data-stu-id="2fd71-221">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="2fd71-222">O código a seguir mostra os valores de chave para as chaves substituídas:</span><span class="sxs-lookup"><span data-stu-id="2fd71-222">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="2fd71-223">O comando a seguir funciona para testar a substituição da chave:</span><span class="sxs-lookup"><span data-stu-id="2fd71-223">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<!-- Run the following command to test the key replacement: -->

<span data-ttu-id="2fd71-224">Observação: no momento, `=` não pode ser usado para definir valores de substituição de chave com um único traço `-` .</span><span class="sxs-lookup"><span data-stu-id="2fd71-224">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="2fd71-225">Consulte [este problema do GitHub](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="2fd71-225">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="2fd71-226">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="2fd71-226">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="2fd71-227">A `CreateDefaultBuilder` chamada do método `AddCommandLine` não inclui opções mapeadas e não há como passar o dicionário de mapeamento de opção para `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2fd71-227">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2fd71-228">A solução não é passar os argumentos para `CreateDefaultBuilder` , mas sim permitir que o `ConfigurationBuilder` método do método `AddCommandLine` processe os argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="2fd71-228">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="2fd71-229">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="2fd71-229">Hierarchical configuration data</span></span>

<span data-ttu-id="2fd71-230">A API de configuração lê dados de configuração hierárquicas mesclando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-230">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="2fd71-231">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém os seguintes *appsettings.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="2fd71-231">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="2fd71-232">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações:</span><span class="sxs-lookup"><span data-stu-id="2fd71-232">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2fd71-233">A maneira preferida de ler dados de configuração hierárquicos é usar o padrão de opções.</span><span class="sxs-lookup"><span data-stu-id="2fd71-233">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="2fd71-234">Para obter mais informações, consulte [associar dados de configuração hierárquica](#optpat) neste documento.</span><span class="sxs-lookup"><span data-stu-id="2fd71-234">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="2fd71-235">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-235"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="2fd71-236">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection).</span><span class="sxs-lookup"><span data-stu-id="2fd71-236">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="2fd71-237">Chaves de configuração e valores</span><span class="sxs-lookup"><span data-stu-id="2fd71-237">Configuration keys and values</span></span>

<span data-ttu-id="2fd71-238">Chaves de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-238">Configuration keys:</span></span>

* <span data-ttu-id="2fd71-239">Não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2fd71-239">Are case-insensitive.</span></span> <span data-ttu-id="2fd71-240">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="2fd71-240">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="2fd71-241">Se uma chave e um valor for definido em mais de um provedor de configuração, o valor do último provedor adicionado será usado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-241">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="2fd71-242">Para obter mais informações, consulte [configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="2fd71-242">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="2fd71-243">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="2fd71-243">Hierarchical keys</span></span>
  * <span data-ttu-id="2fd71-244">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="2fd71-244">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="2fd71-245">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="2fd71-245">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="2fd71-246">Um sublinhado duplo, `__` , tem suporte em todas as plataformas e é automaticamente convertido em dois-pontos `:` .</span><span class="sxs-lookup"><span data-stu-id="2fd71-246">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="2fd71-247">Em Azure Key Vault, as chaves hierárquicas usam `--` como um separador.</span><span class="sxs-lookup"><span data-stu-id="2fd71-247">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="2fd71-248">O [provedor de configuração Azure Key Vault](xref:security/key-vault-configuration) substitui automaticamente `--` por um `:` quando os segredos são carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-248">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="2fd71-249">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-249">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2fd71-250">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#boa).</span><span class="sxs-lookup"><span data-stu-id="2fd71-250">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="2fd71-251">Valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-251">Configuration values:</span></span>

* <span data-ttu-id="2fd71-252">São cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="2fd71-252">Are strings.</span></span>
* <span data-ttu-id="2fd71-253">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="2fd71-253">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="2fd71-254">Provedores de configuração</span><span class="sxs-lookup"><span data-stu-id="2fd71-254">Configuration providers</span></span>

<span data-ttu-id="2fd71-255">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2fd71-255">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="2fd71-256">Provedor</span><span class="sxs-lookup"><span data-stu-id="2fd71-256">Provider</span></span> | <span data-ttu-id="2fd71-257">Fornece a configuração de </span><span class="sxs-lookup"><span data-stu-id="2fd71-257">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="2fd71-258">Provedor de configuração de Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2fd71-258">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="2fd71-259">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="2fd71-259">Azure Key Vault</span></span> |
| [<span data-ttu-id="2fd71-260">Provedor de configuração de Azure App</span><span class="sxs-lookup"><span data-stu-id="2fd71-260">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="2fd71-261">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="2fd71-261">Azure App Configuration</span></span> |
| [<span data-ttu-id="2fd71-262">Provedor de configuração de linha de comando</span><span class="sxs-lookup"><span data-stu-id="2fd71-262">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="2fd71-263">Parâmetros da linha de comando</span><span class="sxs-lookup"><span data-stu-id="2fd71-263">Command-line parameters</span></span> |
| [<span data-ttu-id="2fd71-264">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="2fd71-264">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="2fd71-265">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="2fd71-265">Custom source</span></span> |
| [<span data-ttu-id="2fd71-266">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="2fd71-266">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="2fd71-267">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="2fd71-267">Environment variables</span></span> |
| [<span data-ttu-id="2fd71-268">Provedor de configuração de arquivo</span><span class="sxs-lookup"><span data-stu-id="2fd71-268">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="2fd71-269">Arquivos INI, JSON e XML</span><span class="sxs-lookup"><span data-stu-id="2fd71-269">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="2fd71-270">Provedor de configuração de chave por arquivo</span><span class="sxs-lookup"><span data-stu-id="2fd71-270">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="2fd71-271">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="2fd71-271">Directory files</span></span> |
| [<span data-ttu-id="2fd71-272">Provedor de configuração de memória</span><span class="sxs-lookup"><span data-stu-id="2fd71-272">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="2fd71-273">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="2fd71-273">In-memory collections</span></span> |
| [<span data-ttu-id="2fd71-274">Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="2fd71-274">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="2fd71-275">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="2fd71-275">File in the user profile directory</span></span> |

<span data-ttu-id="2fd71-276">As fontes de configuração são lidas na ordem em que seus provedores de configuração são especificados.</span><span class="sxs-lookup"><span data-stu-id="2fd71-276">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="2fd71-277">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="2fd71-277">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="2fd71-278">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="2fd71-278">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="2fd71-279">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="2fd71-279">*appsettings.json*</span></span>
1. <span data-ttu-id="2fd71-280">*appSettings*. `Environment` . *JSON*</span><span class="sxs-lookup"><span data-stu-id="2fd71-280">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="2fd71-281">Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="2fd71-281">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="2fd71-282">Variáveis de ambiente usando o [provedor de configuração de variáveis de ambiente](#evcp).</span><span class="sxs-lookup"><span data-stu-id="2fd71-282">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="2fd71-283">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2fd71-283">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="2fd71-284">Uma prática comum é adicionar o provedor de configuração de linha de comando pela última vez em uma série de provedores para permitir que argumentos de linha de comando substituam a configuração definida pelos outros provedores.</span><span class="sxs-lookup"><span data-stu-id="2fd71-284">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="2fd71-285">A sequência anterior de provedores é usada na [configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="2fd71-285">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="2fd71-286">Prefixos de cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="2fd71-286">Connection string prefixes</span></span>

<span data-ttu-id="2fd71-287">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="2fd71-287">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="2fd71-288">Essas cadeias de conexão estão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-288">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="2fd71-289">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo com a [configuração padrão](#default) ou quando nenhum prefixo é fornecido para `AddEnvironmentVariables` .</span><span class="sxs-lookup"><span data-stu-id="2fd71-289">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="2fd71-290">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="2fd71-290">Connection string prefix</span></span> | <span data-ttu-id="2fd71-291">Provedor</span><span class="sxs-lookup"><span data-stu-id="2fd71-291">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="2fd71-292">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="2fd71-292">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="2fd71-293">MySQL</span><span class="sxs-lookup"><span data-stu-id="2fd71-293">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="2fd71-294">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="2fd71-294">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="2fd71-295">SQL Server</span><span class="sxs-lookup"><span data-stu-id="2fd71-295">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="2fd71-296">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="2fd71-296">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="2fd71-297">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="2fd71-297">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="2fd71-298">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="2fd71-298">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="2fd71-299">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="2fd71-299">Environment variable key</span></span> | <span data-ttu-id="2fd71-300">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="2fd71-300">Converted configuration key</span></span> | <span data-ttu-id="2fd71-301">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="2fd71-301">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2fd71-302">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="2fd71-302">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2fd71-303">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2fd71-303">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2fd71-304">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="2fd71-304">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2fd71-305">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2fd71-305">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2fd71-306">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2fd71-306">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2fd71-307">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2fd71-307">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2fd71-308">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2fd71-308">Value: `System.Data.SqlClient`</span></span>  |

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="2fd71-309">Provedor de configuração JSON</span><span class="sxs-lookup"><span data-stu-id="2fd71-309">JSON configuration provider</span></span>

<span data-ttu-id="2fd71-310">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor de arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="2fd71-310">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="2fd71-311">Sobrecargas podem especificar:</span><span class="sxs-lookup"><span data-stu-id="2fd71-311">Overloads can specify:</span></span>

* <span data-ttu-id="2fd71-312">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="2fd71-312">Whether the file is optional.</span></span>
* <span data-ttu-id="2fd71-313">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-313">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="2fd71-314">Considere o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fd71-314">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="2fd71-315">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="2fd71-315">The preceding code:</span></span>

* <span data-ttu-id="2fd71-316">Configura o provedor de configuração JSON para carregar o *MyConfig.jsno* arquivo com as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="2fd71-316">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="2fd71-317">`optional: true`: O arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="2fd71-317">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="2fd71-318">`reloadOnChange: true`: O arquivo é recarregado quando as alterações são salvas.</span><span class="sxs-lookup"><span data-stu-id="2fd71-318">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="2fd71-319">Lê os [provedores de configuração padrão](#default) antes da *MyConfig.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-319">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="2fd71-320">As configurações no *MyConfig.jsna* configuração de substituição de arquivo nos provedores de configuração padrão, incluindo o [provedor de configuração de variáveis de ambiente](#evcp) e o provedor de configuração de linha de [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2fd71-320">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2fd71-321">Normalmente, você ***não*** deseja que um arquivo JSON personalizado substitua valores definidos no [provedor de configuração de variáveis de ambiente](#evcp) e no provedor de configuração de linha de [comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2fd71-321">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2fd71-322">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-322">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="2fd71-323">No código anterior, as configurações na *MyConfig.jsem* e *myconfig*. `Environment` . arquivos *JSON* :</span><span class="sxs-lookup"><span data-stu-id="2fd71-323">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="2fd71-324">Substitua as configurações no *appsettings.jsem* e *appSettings*. `Environment` . arquivos *JSON* .</span><span class="sxs-lookup"><span data-stu-id="2fd71-324">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="2fd71-325">São substituídos por configurações no [provedor de configuração de variáveis de ambiente](#evcp) e no [provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2fd71-325">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2fd71-326">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém os seguintes *MyConfig.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="2fd71-326">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="2fd71-327">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="2fd71-327">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="2fd71-328">Provedor de configuração de arquivo</span><span class="sxs-lookup"><span data-stu-id="2fd71-328">File configuration provider</span></span>

<span data-ttu-id="2fd71-329"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="2fd71-329"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="2fd71-330">Os seguintes provedores de configuração derivam de `FileConfigurationProvider` :</span><span class="sxs-lookup"><span data-stu-id="2fd71-330">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="2fd71-331">Provedor de configuração INI</span><span class="sxs-lookup"><span data-stu-id="2fd71-331">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="2fd71-332">Provedor de configuração JSON</span><span class="sxs-lookup"><span data-stu-id="2fd71-332">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="2fd71-333">Provedor de configuração XML</span><span class="sxs-lookup"><span data-stu-id="2fd71-333">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="2fd71-334">Provedor de configuração INI</span><span class="sxs-lookup"><span data-stu-id="2fd71-334">INI configuration provider</span></span>

<span data-ttu-id="2fd71-335">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="2fd71-335">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="2fd71-336">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-336">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="2fd71-337">No código anterior, as configurações no *MyIniConfig.ini* e *MyIniConfig*. `Environment` . os arquivos *ini* são substituídos pelas configurações no:</span><span class="sxs-lookup"><span data-stu-id="2fd71-337">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="2fd71-338">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="2fd71-338">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="2fd71-339">[Provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2fd71-339">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2fd71-340">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo de *MyIniConfig.ini* :</span><span class="sxs-lookup"><span data-stu-id="2fd71-340">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="2fd71-341">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="2fd71-341">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="2fd71-342">Provedor de configuração XML</span><span class="sxs-lookup"><span data-stu-id="2fd71-342">XML configuration provider</span></span>

<span data-ttu-id="2fd71-343">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="2fd71-343">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="2fd71-344">O código a seguir limpa todos os provedores de configuração e adiciona vários provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-344">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="2fd71-345">No código anterior, as configurações no *MyXMLFile.xml* e *MyXMLFile*. `Environment` . os arquivos *XML* são substituídos pelas configurações no:</span><span class="sxs-lookup"><span data-stu-id="2fd71-345">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="2fd71-346">Provedor de configuração de variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="2fd71-346">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="2fd71-347">[Provedor de configuração de linha de comando](#clcp).</span><span class="sxs-lookup"><span data-stu-id="2fd71-347">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="2fd71-348">O [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contém o seguinte arquivo de *MyXMLFile.xml* :</span><span class="sxs-lookup"><span data-stu-id="2fd71-348">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="2fd71-349">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe várias das configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="2fd71-349">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2fd71-350">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="2fd71-350">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="2fd71-351">O código a seguir lê o arquivo de configuração anterior e exibe as chaves e valores:</span><span class="sxs-lookup"><span data-stu-id="2fd71-351">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="2fd71-352">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="2fd71-352">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="2fd71-353">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="2fd71-353">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2fd71-354">key:attribute</span><span class="sxs-lookup"><span data-stu-id="2fd71-354">key:attribute</span></span>
* <span data-ttu-id="2fd71-355">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="2fd71-355">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="2fd71-356">Provedor de configuração de chave por arquivo</span><span class="sxs-lookup"><span data-stu-id="2fd71-356">Key-per-file configuration provider</span></span>

<span data-ttu-id="2fd71-357">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-357">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="2fd71-358">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-358">The key is the file name.</span></span> <span data-ttu-id="2fd71-359">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-359">The value contains the file's contents.</span></span> <span data-ttu-id="2fd71-360">O provedor de configuração de chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="2fd71-360">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="2fd71-361">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-361">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="2fd71-362">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="2fd71-362">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="2fd71-363">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="2fd71-363">Overloads permit specifying:</span></span>

* <span data-ttu-id="2fd71-364">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="2fd71-364">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="2fd71-365">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="2fd71-365">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="2fd71-366">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-366">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="2fd71-367">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-367">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="2fd71-368">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2fd71-368">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="2fd71-369">Provedor de configuração de memória</span><span class="sxs-lookup"><span data-stu-id="2fd71-369">Memory configuration provider</span></span>

<span data-ttu-id="2fd71-370">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-370">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="2fd71-371">O código a seguir adiciona uma coleção de memória ao sistema de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-371">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="2fd71-372">O código a seguir do [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) exibe as configurações anteriores:</span><span class="sxs-lookup"><span data-stu-id="2fd71-372">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="2fd71-373">No código anterior, `config.AddInMemoryCollection(Dict)` é adicionado após os [provedores de configuração padrão](#default).</span><span class="sxs-lookup"><span data-stu-id="2fd71-373">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="2fd71-374">Para obter um exemplo de como ordenar os provedores de configuração, consulte [provedor de configuração JSON](#jcp).</span><span class="sxs-lookup"><span data-stu-id="2fd71-374">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="2fd71-375">Consulte [associar uma matriz](#boa) para outro exemplo usando `MemoryConfigurationProvider` .</span><span class="sxs-lookup"><span data-stu-id="2fd71-375">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="2fd71-376">GetValue</span><span class="sxs-lookup"><span data-stu-id="2fd71-376">GetValue</span></span>

<span data-ttu-id="2fd71-377">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrai um único valor da configuração com uma chave especificada e converte-o no tipo especificado:</span><span class="sxs-lookup"><span data-stu-id="2fd71-377">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="2fd71-378">No código anterior, se `NumberKey` não for encontrado na configuração, o valor padrão de `99` será usado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-378">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="2fd71-379">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="2fd71-379">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="2fd71-380">Para os exemplos a seguir, considere o seguinte *MySubsection.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="2fd71-380">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="2fd71-381">O código a seguir adiciona *MySubsection.js* aos provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-381">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="2fd71-382">GetSection</span><span class="sxs-lookup"><span data-stu-id="2fd71-382">GetSection</span></span>

<span data-ttu-id="2fd71-383">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) retorna uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="2fd71-383">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="2fd71-384">O código a seguir retorna valores para `section1` :</span><span class="sxs-lookup"><span data-stu-id="2fd71-384">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="2fd71-385">O código a seguir retorna valores para `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="2fd71-385">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="2fd71-386">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-386">`GetSection` never returns `null`.</span></span> <span data-ttu-id="2fd71-387">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-387">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="2fd71-388">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="2fd71-388">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="2fd71-389"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="2fd71-389">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="2fd71-390">GetChildren e existe</span><span class="sxs-lookup"><span data-stu-id="2fd71-390">GetChildren and Exists</span></span>

<span data-ttu-id="2fd71-391">O código a seguir chama [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) e retorna valores para `section2:subsection0` :</span><span class="sxs-lookup"><span data-stu-id="2fd71-391">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="2fd71-392">O código anterior chama [ConfigurationExtensions. Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para verificar se a seção existe:</span><span class="sxs-lookup"><span data-stu-id="2fd71-392">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="2fd71-393">Associar uma matriz</span><span class="sxs-lookup"><span data-stu-id="2fd71-393">Bind an array</span></span>

<span data-ttu-id="2fd71-394">O [ConfigurationBinder. bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) dá suporte a matrizes de associação a objetos usando índices de matriz em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-394">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2fd71-395">Qualquer formato de matriz que expõe um segmento de chave numérica é capaz de associar a matriz a uma matriz de classe [poco](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="2fd71-395">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="2fd71-396">Considere *MyArray.js* do download de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="2fd71-396">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="2fd71-397">O código a seguir adiciona *MyArray.js* aos provedores de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-397">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="2fd71-398">O código a seguir lê a configuração e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="2fd71-398">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2fd71-399">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="2fd71-399">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="2fd71-400">Na saída anterior, o índice 3 tem valor `value40` , correspondente a `"4": "value40",` no *MyArray.jsem*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-400">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="2fd71-401">Os índices de matriz associados são contínuos e não associados ao índice de chave de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-401">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="2fd71-402">O associador de configuração não é capaz de ligar valores nulos ou criar entradas nulas em objetos associados</span><span class="sxs-lookup"><span data-stu-id="2fd71-402">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="2fd71-403">O código a seguir carrega a `array:entries` configuração com o <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> método de extensão:</span><span class="sxs-lookup"><span data-stu-id="2fd71-403">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="2fd71-404">O código a seguir lê a configuração no `arrayDict` `Dictionary` e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="2fd71-404">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2fd71-405">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="2fd71-405">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="2fd71-406">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-406">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="2fd71-407">Quando os dados de configuração que contêm uma matriz são associados, os índices de matriz nas chaves de configuração são usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="2fd71-407">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="2fd71-408">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="2fd71-408">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="2fd71-409">O item de configuração ausente para &num; o índice 3 pode ser fornecido antes da associação à `ArrayExample` instância por qualquer provedor de configuração que leia o &num; par chave/valor do índice 3.</span><span class="sxs-lookup"><span data-stu-id="2fd71-409">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="2fd71-410">Considere o seguinte *Value3.jsno* arquivo do download de exemplo:</span><span class="sxs-lookup"><span data-stu-id="2fd71-410">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="2fd71-411">O código a seguir inclui a configuração para o *Value3.jsno* e no `arrayDict` `Dictionary` :</span><span class="sxs-lookup"><span data-stu-id="2fd71-411">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="2fd71-412">O código a seguir lê a configuração anterior e exibe os valores:</span><span class="sxs-lookup"><span data-stu-id="2fd71-412">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="2fd71-413">O código anterior retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="2fd71-413">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="2fd71-414">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="2fd71-414">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="2fd71-415">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="2fd71-415">Custom configuration provider</span></span>

<span data-ttu-id="2fd71-416">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="2fd71-416">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="2fd71-417">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="2fd71-417">The provider has the following characteristics:</span></span>

* <span data-ttu-id="2fd71-418">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-418">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="2fd71-419">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-419">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="2fd71-420">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="2fd71-420">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="2fd71-421">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="2fd71-421">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="2fd71-422">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-422">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="2fd71-423">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="2fd71-423">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="2fd71-424">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-424">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="2fd71-425">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="2fd71-425">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="2fd71-426">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-426">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="2fd71-427">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-427">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="2fd71-428">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-428">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="2fd71-429">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-429">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="2fd71-430">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="2fd71-430">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="2fd71-431">Como [as chaves de configuração](#keys)não diferenciam maiúsculas de minúsculas, o dicionário usado para inicializar o banco de dados é criado com o comparador que não diferencia maiúsculas de minúsculas ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="2fd71-431">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="2fd71-432">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-432">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="2fd71-433">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-433">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="2fd71-434">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-434">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="2fd71-435">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-435">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="2fd71-436">Configuração de acesso na inicialização</span><span class="sxs-lookup"><span data-stu-id="2fd71-436">Access configuration in Startup</span></span>

<span data-ttu-id="2fd71-437">O código a seguir exibe dados de configuração em `Startup` métodos:</span><span class="sxs-lookup"><span data-stu-id="2fd71-437">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="2fd71-438">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="2fd71-438">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="2fd71-439">Configuração de acesso em Razor páginas</span><span class="sxs-lookup"><span data-stu-id="2fd71-439">Access configuration in Razor Pages</span></span>

<span data-ttu-id="2fd71-440">O código a seguir exibe os dados de configuração em uma Razor página:</span><span class="sxs-lookup"><span data-stu-id="2fd71-440">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="2fd71-441">No código a seguir, `MyOptions` é adicionado ao contêiner de serviço com <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> e associado à configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-441">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="2fd71-442">A marcação a seguir usa a [`@inject`](xref:mvc/views/razor#inject) Razor diretiva para resolver e exibir os valores de opções:</span><span class="sxs-lookup"><span data-stu-id="2fd71-442">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="2fd71-443">Acessar a configuração em um arquivo de exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="2fd71-443">Access configuration in a MVC view file</span></span>

<span data-ttu-id="2fd71-444">O código a seguir exibe os dados de configuração em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="2fd71-444">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="2fd71-445">Configurar opções com um delegado</span><span class="sxs-lookup"><span data-stu-id="2fd71-445">Configure options with a delegate</span></span>

<span data-ttu-id="2fd71-446">As opções configuradas em um delegado substituem valores definidos nos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-446">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="2fd71-447">A configuração de opções com um delegado é demonstrada como o exemplo 2 no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-447">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="2fd71-448">No código a seguir, um <xref:Microsoft.Extensions.Options.IConfigureOptions%601> serviço é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="2fd71-448">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="2fd71-449">Ele usa um delegado para configurar valores para `MyOptions` :</span><span class="sxs-lookup"><span data-stu-id="2fd71-449">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="2fd71-450">O código a seguir exibe os valores de opções:</span><span class="sxs-lookup"><span data-stu-id="2fd71-450">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="2fd71-451">No exemplo anterior, os valores de `Option1` e `Option2` são especificados em *appsettings.js* e, em seguida, substituídos pelo delegado configurado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-451">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="2fd71-452">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="2fd71-452">Host versus app configuration</span></span>

<span data-ttu-id="2fd71-453">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-453">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="2fd71-454">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-454">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="2fd71-455">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="2fd71-455">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="2fd71-456">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-456">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="2fd71-457">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-457">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="2fd71-458">Configuração de host padrão</span><span class="sxs-lookup"><span data-stu-id="2fd71-458">Default host configuration</span></span>

<span data-ttu-id="2fd71-459">Para obter detalhes sobre a configuração padrão ao usar o [host da Web](xref:fundamentals/host/web-host), confira a [versão do ASP.NET Core 2.2 deste tópico](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="2fd71-459">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="2fd71-460">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="2fd71-460">Host configuration is provided from:</span></span>
  * <span data-ttu-id="2fd71-461">Variáveis de ambiente prefixadas com `DOTNET_` (por exemplo, `DOTNET_ENVIRONMENT` ) usando o [provedor de configuração de variáveis de ambiente](#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="2fd71-461">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="2fd71-462">O prefixo (`DOTNET_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-462">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="2fd71-463">Argumentos de linha de comando usando o [provedor de configuração de linha de comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2fd71-463">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="2fd71-464">A configuração padrão do host da Web foi estabelecida (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="2fd71-464">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="2fd71-465">O Kestrel é usado como o servidor Web e configurado usando provedores de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-465">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="2fd71-466">Adicione o middleware de filtragem de host.</span><span class="sxs-lookup"><span data-stu-id="2fd71-466">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="2fd71-467">Adicione o middleware de cabeçalhos encaminhados se a variável de ambiente `ASPNETCORE_FORWARDEDHEADERS_ENABLED` for definida para `true`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-467">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="2fd71-468">Habilite a integração de IIS.</span><span class="sxs-lookup"><span data-stu-id="2fd71-468">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="2fd71-469">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="2fd71-469">Other configuration</span></span>

<span data-ttu-id="2fd71-470">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-470">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="2fd71-471">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="2fd71-471">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="2fd71-472">*launch.jsem* / As *launchSettings.jsno* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="2fd71-472">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="2fd71-473">Em <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="2fd71-473">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="2fd71-474">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2fd71-474">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="2fd71-475">*web.config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="2fd71-475">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="2fd71-476">As variáveis de ambiente definidas no *launchSettings.js* substituir aquelas definidas no ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="2fd71-476">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="2fd71-477">Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="2fd71-477">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="2fd71-478">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="2fd71-478">Add configuration from an external assembly</span></span>

<span data-ttu-id="2fd71-479">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-479">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="2fd71-480">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-480">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2fd71-481">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2fd71-481">Additional resources</span></span>

* [<span data-ttu-id="2fd71-482">Código-fonte de configuração</span><span class="sxs-lookup"><span data-stu-id="2fd71-482">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2fd71-483">A configuração de aplicativos no ASP.NET Core se baseia em pares chave-valor estabelecidos por *provedores de configuração*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-483">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="2fd71-484">Os provedores de configuração leem os dados de configuração em pares chave-valor de várias fontes de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-484">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="2fd71-485">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="2fd71-485">Azure Key Vault</span></span>
* <span data-ttu-id="2fd71-486">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="2fd71-486">Azure App Configuration</span></span>
* <span data-ttu-id="2fd71-487">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="2fd71-487">Command-line arguments</span></span>
* <span data-ttu-id="2fd71-488">Provedores personalizados (instalados ou criados)</span><span class="sxs-lookup"><span data-stu-id="2fd71-488">Custom providers (installed or created)</span></span>
* <span data-ttu-id="2fd71-489">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="2fd71-489">Directory files</span></span>
* <span data-ttu-id="2fd71-490">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="2fd71-490">Environment variables</span></span>
* <span data-ttu-id="2fd71-491">Objetos do .NET na memória</span><span class="sxs-lookup"><span data-stu-id="2fd71-491">In-memory .NET objects</span></span>
* <span data-ttu-id="2fd71-492">Arquivos de configurações</span><span class="sxs-lookup"><span data-stu-id="2fd71-492">Settings files</span></span>

<span data-ttu-id="2fd71-493">Os pacotes de configuração para cenários comuns do provedor de configuração ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) são incluídos no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="2fd71-493">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2fd71-494">Os exemplos de código a seguir e no aplicativo de exemplo usam o namespace <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="2fd71-494">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="2fd71-495">O *padrão de opções* é uma extensão dos conceitos de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="2fd71-495">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="2fd71-496">As opções usam classes para representar grupos de configurações relacionadas.</span><span class="sxs-lookup"><span data-stu-id="2fd71-496">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="2fd71-497">Para obter mais informações, consulte <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-497">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="2fd71-498">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2fd71-498">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="2fd71-499">Configuração do host versus do aplicativo</span><span class="sxs-lookup"><span data-stu-id="2fd71-499">Host versus app configuration</span></span>

<span data-ttu-id="2fd71-500">Antes do aplicativo ser configurado e iniciado, um *host* é configurado e iniciado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-500">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="2fd71-501">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-501">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="2fd71-502">O aplicativo e o host são configurados usando os provedores de configuração descritos neste tópico.</span><span class="sxs-lookup"><span data-stu-id="2fd71-502">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="2fd71-503">Os pares chave-valor de configuração do host também estão incluídos na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-503">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="2fd71-504">Para saber mais sobre como os provedores de configuração são usados quando o host for compilado, e como as fontes de configuração afetam o host e a configuração, confira <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-504">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="2fd71-505">Outra configuração</span><span class="sxs-lookup"><span data-stu-id="2fd71-505">Other configuration</span></span>

<span data-ttu-id="2fd71-506">Este tópico pertence apenas à *configuração do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-506">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="2fd71-507">Outros aspectos da execução e Hospedagem de aplicativos ASP.NET Core são configurados usando arquivos de configuração não abordados neste tópico:</span><span class="sxs-lookup"><span data-stu-id="2fd71-507">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="2fd71-508">*launch.jsem* / As *launchSettings.jsno* são arquivos de configuração de ferramentas para o ambiente de desenvolvimento, descritos:</span><span class="sxs-lookup"><span data-stu-id="2fd71-508">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="2fd71-509">Em <xref:fundamentals/environments#development> .</span><span class="sxs-lookup"><span data-stu-id="2fd71-509">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="2fd71-510">No conjunto de documentação, onde os arquivos são usados para configurar aplicativos ASP.NET Core para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2fd71-510">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="2fd71-511">*web.config* é um arquivo de configuração de servidor, descrito nos seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="2fd71-511">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="2fd71-512">Para obter mais informações sobre como migrar a configuração de aplicativo de versões anteriores do ASP.NET, consulte <xref:migration/proper-to-2x/index#store-configurations> .</span><span class="sxs-lookup"><span data-stu-id="2fd71-512">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="2fd71-513">Configuração padrão</span><span class="sxs-lookup"><span data-stu-id="2fd71-513">Default configuration</span></span>

<span data-ttu-id="2fd71-514">Aplicativos Web baseados em modelos [dotnet new](/dotnet/core/tools/dotnet-new) do ASP.NET Core chamam <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ao criar um host.</span><span class="sxs-lookup"><span data-stu-id="2fd71-514">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="2fd71-515">`CreateDefaultBuilder` fornece a configuração padrão para o aplicativo na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="2fd71-515">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="2fd71-516">O seguinte se aplica a aplicativos que usam o [host da Web](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="2fd71-516">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="2fd71-517">Para obter detalhes sobre a configuração padrão ao usar o [host genérico](xref:fundamentals/host/generic-host), confira [versão mais recente deste tópico](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2fd71-517">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="2fd71-518">A configuração do host é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="2fd71-518">Host configuration is provided from:</span></span>
  * <span data-ttu-id="2fd71-519">Variáveis ​​de ambiente prefixadas com `ASPNETCORE_` (por exemplo, `ASPNETCORE_ENVIRONMENT`) usando o [Provedor de Configuração de Variáveis ​​de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2fd71-519">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="2fd71-520">O prefixo (`ASPNETCORE_`) é removido durante o carregamento dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-520">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="2fd71-521">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2fd71-521">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="2fd71-522">A configuração do aplicativo é fornecida de:</span><span class="sxs-lookup"><span data-stu-id="2fd71-522">App configuration is provided from:</span></span>
  * <span data-ttu-id="2fd71-523">*appsettings.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2fd71-523">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="2fd71-524">*appsettings.{Environment}.json* usando o [Provedor de Configuração do Arquivo](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2fd71-524">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="2fd71-525">[Gerenciador de Segredo](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development` usando o assembly de entrada.</span><span class="sxs-lookup"><span data-stu-id="2fd71-525">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="2fd71-526">Variáveis de ambiente usando o [Provedor de Configuração de Variáveis de Ambiente](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2fd71-526">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="2fd71-527">Argumentos de linha de comando usando o [Provedor de Configuração de Linha de Comando](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="2fd71-527">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="2fd71-528">Segurança</span><span class="sxs-lookup"><span data-stu-id="2fd71-528">Security</span></span>

<span data-ttu-id="2fd71-529">Adote as seguintes práticas para proteger dados de configuração confidenciais:</span><span class="sxs-lookup"><span data-stu-id="2fd71-529">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="2fd71-530">Nunca armazene senhas ou outros dados confidenciais no código do provedor de configuração ou nos arquivos de configuração de texto sem formatação.</span><span class="sxs-lookup"><span data-stu-id="2fd71-530">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="2fd71-531">Não use segredos de produção em ambientes de teste ou de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2fd71-531">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="2fd71-532">Especifique segredos fora do projeto para que eles não sejam acidentalmente comprometidos com um repositório de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="2fd71-532">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="2fd71-533">Para obter mais informações, consulte estes tópicos:</span><span class="sxs-lookup"><span data-stu-id="2fd71-533">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="2fd71-534"><xref:security/app-secrets>: Inclui conselhos sobre como usar variáveis de ambiente para armazenar dados confidenciais.</span><span class="sxs-lookup"><span data-stu-id="2fd71-534"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="2fd71-535">O Gerenciador de Segredo usa o Provedor de Configuração de Arquivo para armazenar segredos do usuário em um arquivo JSON no sistema local.</span><span class="sxs-lookup"><span data-stu-id="2fd71-535">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="2fd71-536">O Provedor de Configuração de Arquivo será descrito mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="2fd71-536">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="2fd71-537">O [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) armazena os segredos do aplicativo com segurança para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2fd71-537">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="2fd71-538">Para obter mais informações, consulte <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-538">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="2fd71-539">Dados de configuração hierárquica</span><span class="sxs-lookup"><span data-stu-id="2fd71-539">Hierarchical configuration data</span></span>

<span data-ttu-id="2fd71-540">A API de Configuração é capaz de manter dados de configuração hierárquica nivelando os dados hierárquicos com o uso de um delimitador nas chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-540">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="2fd71-541">No seguinte arquivo JSON, há quatro chaves em uma hierarquia estruturada com duas seções:</span><span class="sxs-lookup"><span data-stu-id="2fd71-541">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="2fd71-542">Quando o arquivo é lido na configuração, ocorre a criação de chaves exclusivas para manter a estrutura hierárquica de dados original da fonte de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-542">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="2fd71-543">As seções e as chaves são niveladas usando dois-pontos (`:`) para manter a estrutura original:</span><span class="sxs-lookup"><span data-stu-id="2fd71-543">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="2fd71-544">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2fd71-544">section0:key0</span></span>
* <span data-ttu-id="2fd71-545">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2fd71-545">section0:key1</span></span>
* <span data-ttu-id="2fd71-546">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2fd71-546">section1:key0</span></span>
* <span data-ttu-id="2fd71-547">section1:key1</span><span class="sxs-lookup"><span data-stu-id="2fd71-547">section1:key1</span></span>

<span data-ttu-id="2fd71-548">Os métodos <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> e <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> estão disponíveis para isolar as seções e os filhos de uma seção nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-548"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="2fd71-549">Esses métodos serão descritos posteriormente em [GetSection, GetChildren e Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="2fd71-549">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="2fd71-550">Convenções</span><span class="sxs-lookup"><span data-stu-id="2fd71-550">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="2fd71-551">Origens e provedores</span><span class="sxs-lookup"><span data-stu-id="2fd71-551">Sources and providers</span></span>

<span data-ttu-id="2fd71-552">Na inicialização do aplicativo, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-552">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="2fd71-553">Os provedores de configuração que implementam a detecção de alteração podem recarregar a configuração quando uma configuração subjacente for alterada.</span><span class="sxs-lookup"><span data-stu-id="2fd71-553">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="2fd71-554">Por exemplo, o Provedor de configuração do arquivo (descrito mais adiante neste tópico) e o [Provedor de configuração do Azure Key Vault](xref:security/key-vault-configuration) implementam a detecção de alteração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-554">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="2fd71-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> está disponível no contêiner [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2fd71-556"><xref:Microsoft.Extensions.Configuration.IConfiguration>pode ser injetado em uma Razor página <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ou MVC <xref:Microsoft.AspNetCore.Mvc.Controller> para obter a configuração da classe.</span><span class="sxs-lookup"><span data-stu-id="2fd71-556"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="2fd71-557">Nos exemplos a seguir, o `_config` campo é usado para acessar os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-557">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="2fd71-558">Os provedores de configuração não podem utilizar a DI, pois ela não é disponibilizada quando eles são configurados pelo host.</span><span class="sxs-lookup"><span data-stu-id="2fd71-558">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="2fd71-559">simétricas</span><span class="sxs-lookup"><span data-stu-id="2fd71-559">Keys</span></span>

<span data-ttu-id="2fd71-560">As chaves de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="2fd71-560">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="2fd71-561">As chaves não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="2fd71-561">Keys are case-insensitive.</span></span> <span data-ttu-id="2fd71-562">Por exemplo, `ConnectionString` e `connectionstring` são tratados como chaves equivalentes.</span><span class="sxs-lookup"><span data-stu-id="2fd71-562">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="2fd71-563">Se um valor para a mesma chave for definido pelos mesmos provedores de configuração, ou por outros, o último valor definido na chave será o valor usado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-563">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="2fd71-564">Para obter mais informações sobre chaves JSON duplicadas, consulte [este problema do GitHub](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="2fd71-564">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="2fd71-565">Chaves hierárquicas</span><span class="sxs-lookup"><span data-stu-id="2fd71-565">Hierarchical keys</span></span>
  * <span data-ttu-id="2fd71-566">Ao interagir com a API de configuração, um separador de dois-pontos (`:`) funciona em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="2fd71-566">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="2fd71-567">Nas variáveis de ambiente, talvez um separador de dois-pontos não funcione em todas as plataformas.</span><span class="sxs-lookup"><span data-stu-id="2fd71-567">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="2fd71-568">Um sublinhado duplo (`__`) é compatível com todas as plataformas e é convertido automaticamente em dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="2fd71-568">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="2fd71-569">No Azure Key Vault, as chaves hierárquicas usam `--` (dois traços) como separador.</span><span class="sxs-lookup"><span data-stu-id="2fd71-569">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="2fd71-570">Escreva o código para substituir os traços por dois-pontos quando os segredos forem carregados na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-570">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="2fd71-571">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-571">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2fd71-572">A associação de matriz está descrita na seção [Associar uma matriz a uma classe](#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="2fd71-572">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="2fd71-573">Valores</span><span class="sxs-lookup"><span data-stu-id="2fd71-573">Values</span></span>

<span data-ttu-id="2fd71-574">Os valores de configuração adotam as convenções a seguir:</span><span class="sxs-lookup"><span data-stu-id="2fd71-574">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="2fd71-575">Os valores são cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="2fd71-575">Values are strings.</span></span>
* <span data-ttu-id="2fd71-576">Não é possível armazenar valores nulos na configuração ou associá-los a objetos.</span><span class="sxs-lookup"><span data-stu-id="2fd71-576">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="2fd71-577">Provedores</span><span class="sxs-lookup"><span data-stu-id="2fd71-577">Providers</span></span>

<span data-ttu-id="2fd71-578">A tabela a seguir mostra os provedores de configuração disponíveis para aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2fd71-578">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="2fd71-579">Provedor</span><span class="sxs-lookup"><span data-stu-id="2fd71-579">Provider</span></span> | <span data-ttu-id="2fd71-580">Fornece a configuração de &hellip;</span><span class="sxs-lookup"><span data-stu-id="2fd71-580">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="2fd71-581">[Provedor de Configuração do Azure Key Vault](xref:security/key-vault-configuration) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="2fd71-581">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="2fd71-582">Cofre de Chave do Azure</span><span class="sxs-lookup"><span data-stu-id="2fd71-582">Azure Key Vault</span></span> |
| <span data-ttu-id="2fd71-583">[Provedor da Configuração de Aplicativos do Azure](/azure/azure-app-configuration/quickstart-aspnet-core-app) (documentação do Azure)</span><span class="sxs-lookup"><span data-stu-id="2fd71-583">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="2fd71-584">Configuração de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="2fd71-584">Azure App Configuration</span></span> |
| [<span data-ttu-id="2fd71-585">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="2fd71-585">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="2fd71-586">Parâmetros da linha de comando</span><span class="sxs-lookup"><span data-stu-id="2fd71-586">Command-line parameters</span></span> |
| [<span data-ttu-id="2fd71-587">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="2fd71-587">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="2fd71-588">Fonte personalizada</span><span class="sxs-lookup"><span data-stu-id="2fd71-588">Custom source</span></span> |
| [<span data-ttu-id="2fd71-589">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="2fd71-589">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="2fd71-590">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="2fd71-590">Environment variables</span></span> |
| [<span data-ttu-id="2fd71-591">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="2fd71-591">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="2fd71-592">Arquivos (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="2fd71-592">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="2fd71-593">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="2fd71-593">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="2fd71-594">Arquivos de diretório</span><span class="sxs-lookup"><span data-stu-id="2fd71-594">Directory files</span></span> |
| [<span data-ttu-id="2fd71-595">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="2fd71-595">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="2fd71-596">Coleções na memória</span><span class="sxs-lookup"><span data-stu-id="2fd71-596">In-memory collections</span></span> |
| <span data-ttu-id="2fd71-597">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (tópicos de *Segurança*)</span><span class="sxs-lookup"><span data-stu-id="2fd71-597">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="2fd71-598">Arquivo no diretório de perfil do usuário</span><span class="sxs-lookup"><span data-stu-id="2fd71-598">File in the user profile directory</span></span> |

<span data-ttu-id="2fd71-599">Na inicialização, as fontes de configuração são lidas na ordem especificada pelos provedores de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-599">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="2fd71-600">Os provedores de configuração descritos neste tópico são descritos em ordem alfabética, não na ordem em que o código os organiza.</span><span class="sxs-lookup"><span data-stu-id="2fd71-600">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="2fd71-601">Solicite provedores de configuração no código para se adequar às prioridades das fontes de configuração subjacentes que o aplicativo requer.</span><span class="sxs-lookup"><span data-stu-id="2fd71-601">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="2fd71-602">Uma sequência comum de provedores de configuração é:</span><span class="sxs-lookup"><span data-stu-id="2fd71-602">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="2fd71-603">Arquivos (*appsettings.jsem*, *appSettings. { Ambiente}. JSON*, em que `{Environment}` é o ambiente de hospedagem atual do aplicativo)</span><span class="sxs-lookup"><span data-stu-id="2fd71-603">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="2fd71-604">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="2fd71-604">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="2fd71-605">[Segredos do usuário (Gerenciador de Segredo)](xref:security/app-secrets) (apenas no ambiente de desenvolvimento)</span><span class="sxs-lookup"><span data-stu-id="2fd71-605">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="2fd71-606">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="2fd71-606">Environment variables</span></span>
1. <span data-ttu-id="2fd71-607">Argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="2fd71-607">Command-line arguments</span></span>

<span data-ttu-id="2fd71-608">Uma prática comum é posicionar o Provedor de Configuração de Linha de Comando por último em uma série de provedores, a fim de permitir que os argumentos de linha de comando substituam a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="2fd71-608">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="2fd71-609">A sequência anterior de provedores é usada quando um novo Construtor de hosts é inicializado com o `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2fd71-609">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2fd71-610">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2fd71-610">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="2fd71-611">Configurar o construtor de host com UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="2fd71-611">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="2fd71-612">Para configurar o construtor de host, chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> no construtor de host com a configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-612">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="2fd71-613">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="2fd71-613">ConfigureAppConfiguration</span></span>

<span data-ttu-id="2fd71-614">Chame `ConfigureAppConfiguration` ao criar o host para especificar os provedores de configuração do aplicativo, além daqueles adicionados automaticamente por `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="2fd71-614">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="2fd71-615">Substituir a configuração anterior por argumentos de linha de comando</span><span class="sxs-lookup"><span data-stu-id="2fd71-615">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="2fd71-616">Para fornecer a configuração de aplicativos que pode ser substituída por argumentos de linha de comando, chame `AddCommandLine` por último:</span><span class="sxs-lookup"><span data-stu-id="2fd71-616">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="2fd71-617">Remover provedores adicionados por CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="2fd71-617">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="2fd71-618">Para remover os provedores adicionados pelo `CreateDefaultBuilder` , chame [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) no [IConfigurationBuilder. Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) primeiro:</span><span class="sxs-lookup"><span data-stu-id="2fd71-618">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="2fd71-619">Consumir a configuração durante a inicialização do aplicativo</span><span class="sxs-lookup"><span data-stu-id="2fd71-619">Consume configuration during app startup</span></span>

<span data-ttu-id="2fd71-620">a configuração fornecida para o aplicativo no `ConfigureAppConfiguration` está disponível durante a inicialização do aplicativo, incluindo `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-620">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2fd71-621">Para saber mais, confira a seção [Access configuration during startup](#access-configuration-during-startup) (Configuração de acesso durante a inicialização).</span><span class="sxs-lookup"><span data-stu-id="2fd71-621">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="2fd71-622">Provedor de Configuração de Linha de Comando</span><span class="sxs-lookup"><span data-stu-id="2fd71-622">Command-line Configuration Provider</span></span>

<span data-ttu-id="2fd71-623">O <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> carrega a configuração dos pares chave-valor do argumento de linha de comando em runtime.</span><span class="sxs-lookup"><span data-stu-id="2fd71-623">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="2fd71-624">Para ativar a configuração de linha de comando, o método de extensão <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> é chamado em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-624">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2fd71-625">`AddCommandLine` é chamado automaticamente quando `CreateDefaultBuilder(string [])` é chamado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-625">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="2fd71-626">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2fd71-626">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2fd71-627">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="2fd71-627">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2fd71-628">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-628">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="2fd71-629">[Segredos do usuário (Gerenciador de segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2fd71-629">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2fd71-630">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2fd71-630">Environment variables.</span></span>

<span data-ttu-id="2fd71-631">`CreateDefaultBuilder` adiciona o Provedor de Configuração de Linha de Comando por último.</span><span class="sxs-lookup"><span data-stu-id="2fd71-631">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="2fd71-632">Os argumentos de linha de comando passados em runtime substituem a configuração definida por outros provedores.</span><span class="sxs-lookup"><span data-stu-id="2fd71-632">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="2fd71-633">`CreateDefaultBuilder` age quando o host é construído.</span><span class="sxs-lookup"><span data-stu-id="2fd71-633">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="2fd71-634">Portanto, a configuração de linha de comando ativada por `CreateDefaultBuilder` pode afetar como o host é configurado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-634">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="2fd71-635">Para aplicativos baseados nos modelos do ASP.NET Core, `AddCommandLine` já foi chamado pelo `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-635">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2fd71-636">Para adicionar outros provedores de configuração e manter a capacidade de substituir a configuração desses provedores por argumentos de linha de comando, chame os provedores adicionais do aplicativo em `ConfigureAppConfiguration` e chame `AddCommandLine` por último.</span><span class="sxs-lookup"><span data-stu-id="2fd71-636">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="2fd71-637">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="2fd71-637">**Example**</span></span>

<span data-ttu-id="2fd71-638">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-638">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="2fd71-639">Abra um prompt de comando no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="2fd71-639">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="2fd71-640">Forneça um argumento de linha de comando para o comando `dotnet run`, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-640">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="2fd71-641">Quando o aplicativo estiver em execução, abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-641">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2fd71-642">Observe que a saída contém o par chave-valor do argumento de linha de comando de configuração fornecido para `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-642">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="2fd71-643">Argumentos</span><span class="sxs-lookup"><span data-stu-id="2fd71-643">Arguments</span></span>

<span data-ttu-id="2fd71-644">O valor deve vir após um sinal de igual (`=`), ou a chave deve ter um prefixo (`--` ou `/`) quando o valor vier após um espaço.</span><span class="sxs-lookup"><span data-stu-id="2fd71-644">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="2fd71-645">O valor não é necessário se um sinal de igual é usado (por exemplo, `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="2fd71-645">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="2fd71-646">Prefixo da chave</span><span class="sxs-lookup"><span data-stu-id="2fd71-646">Key prefix</span></span>               | <span data-ttu-id="2fd71-647">Exemplo</span><span class="sxs-lookup"><span data-stu-id="2fd71-647">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="2fd71-648">Nenhum prefixo</span><span class="sxs-lookup"><span data-stu-id="2fd71-648">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="2fd71-649">Dois traços (`--`)</span><span class="sxs-lookup"><span data-stu-id="2fd71-649">Two dashes (`--`)</span></span>        | <span data-ttu-id="2fd71-650">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="2fd71-650">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="2fd71-651">Barra (`/`)</span><span class="sxs-lookup"><span data-stu-id="2fd71-651">Forward slash (`/`)</span></span>      | <span data-ttu-id="2fd71-652">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="2fd71-652">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="2fd71-653">No mesmo comando, não combine pares chave-valor do argumento de linha de comando que usam um sinal de igual com pares chave-valor que usam um espaço.</span><span class="sxs-lookup"><span data-stu-id="2fd71-653">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="2fd71-654">Exemplo de comandos:</span><span class="sxs-lookup"><span data-stu-id="2fd71-654">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="2fd71-655">Mapeamentos de comutador</span><span class="sxs-lookup"><span data-stu-id="2fd71-655">Switch mappings</span></span>

<span data-ttu-id="2fd71-656">Os mapeamentos de comutador permitem fornecer a lógica de substituição do nome da chave.</span><span class="sxs-lookup"><span data-stu-id="2fd71-656">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="2fd71-657">Ao criar manualmente a configuração com um <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , forneça um dicionário de substituições de switch para o <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> método.</span><span class="sxs-lookup"><span data-stu-id="2fd71-657">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="2fd71-658">Ao ser usado, o dicionário de mapeamentos de comutador é verificado para oferecer uma chave que corresponda à chave fornecida por um argumento de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="2fd71-658">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="2fd71-659">Se a chave de linha de comando for encontrada no dicionário, o valor do dicionário (a substituição da chave) será passado de volta para definir o par chave-valor na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-659">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="2fd71-660">Um mapeamento de comutador é necessário para qualquer chave de linha de comando prefixada com um traço único (`-`).</span><span class="sxs-lookup"><span data-stu-id="2fd71-660">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="2fd71-661">Regras de chave do dicionário de mapeamentos de comutador:</span><span class="sxs-lookup"><span data-stu-id="2fd71-661">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="2fd71-662">Os comutadores devem começar com um traço (`-`) ou traço duplo (`--`).</span><span class="sxs-lookup"><span data-stu-id="2fd71-662">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="2fd71-663">O dicionário de mapeamentos de comutador chave não deve conter chaves duplicadas.</span><span class="sxs-lookup"><span data-stu-id="2fd71-663">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="2fd71-664">Crie um dicionário de mapeamentos de opção.</span><span class="sxs-lookup"><span data-stu-id="2fd71-664">Create a switch mappings dictionary.</span></span> <span data-ttu-id="2fd71-665">No exemplo a seguir, dois mapeamentos de opção são criados:</span><span class="sxs-lookup"><span data-stu-id="2fd71-665">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="2fd71-666">Quando o host for criado, chame `AddCommandLine` com o dicionário de mapeamentos de opção:</span><span class="sxs-lookup"><span data-stu-id="2fd71-666">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="2fd71-667">Para aplicativos que usam mapeamentos de opção, a chamada `CreateDefaultBuilder` para não deve passar argumentos.</span><span class="sxs-lookup"><span data-stu-id="2fd71-667">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="2fd71-668">A chamada `AddCommandLine` do método `CreateDefaultBuilder` não inclui opções mapeadas, e não é possível passar o dicionário de mapeamento de opções para `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-668">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2fd71-669">A solução não é passar os argumentos para `CreateDefaultBuilder`, mas, em vez disso, permitir que o método `AddCommandLine` do método `ConfigurationBuilder` processe os dois argumentos e o dicionário de mapeamento de opções.</span><span class="sxs-lookup"><span data-stu-id="2fd71-669">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="2fd71-670">Depois que o dicionário de mapeamentos de comutador for criado, ele conterá os dados mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="2fd71-670">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="2fd71-671">Chave</span><span class="sxs-lookup"><span data-stu-id="2fd71-671">Key</span></span>       | <span data-ttu-id="2fd71-672">Valor</span><span class="sxs-lookup"><span data-stu-id="2fd71-672">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="2fd71-673">Se as chaves mapeadas para opção forem usadas ao iniciar o aplicativo, a configuração receberá o valor de configuração na chave fornecida pelo dicionário:</span><span class="sxs-lookup"><span data-stu-id="2fd71-673">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="2fd71-674">Após a execução do comando anterior, a configuração conterá os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="2fd71-674">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="2fd71-675">Chave</span><span class="sxs-lookup"><span data-stu-id="2fd71-675">Key</span></span>               | <span data-ttu-id="2fd71-676">Valor</span><span class="sxs-lookup"><span data-stu-id="2fd71-676">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="2fd71-677">Provedor de Configuração de Variáveis de Ambiente</span><span class="sxs-lookup"><span data-stu-id="2fd71-677">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="2fd71-678">O <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> carrega a configuração dos pares chave-valor da variável de ambiente em runtime.</span><span class="sxs-lookup"><span data-stu-id="2fd71-678">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="2fd71-679">Para ativar a configuração das variáveis de ambiente, chame o método de extensão <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-679">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="2fd71-680">[Azure app serviço](https://azure.microsoft.com/services/app-service/) permite definir variáveis de ambiente no portal do Azure que podem substituir a configuração do aplicativo usando o provedor de configuração de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2fd71-680">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="2fd71-681">Para saber mais, confira [Aplicativos do Azure: substituir a configuração do aplicativo usando o portal do Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="2fd71-681">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="2fd71-682">`AddEnvironmentVariables` é usado para carregar variáveis de ambiente com prefixo `ASPNETCORE_` para [configuração de host](#host-versus-app-configuration) quando um novo construtor de host é inicializado com o [host da Web](xref:fundamentals/host/web-host) e `CreateDefaultBuilder` é chamado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-682">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="2fd71-683">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2fd71-683">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2fd71-684">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="2fd71-684">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2fd71-685">Configuração de aplicativo em variáveis de ambiente sem prefixos chamando `AddEnvironmentVariables` sem um prefixo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-685">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="2fd71-686">Configuração opcional de arquivos *appsettings.json* e *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-686">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="2fd71-687">[Segredos do usuário (Gerenciador de segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2fd71-687">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2fd71-688">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="2fd71-688">Command-line arguments.</span></span>

<span data-ttu-id="2fd71-689">O Provedor de Configuração de Variáveis de Ambiente é chamado depois que a configuração é estabelecida por meio dos segredos do usuário e dos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-689">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="2fd71-690">Chamar o provedor nessa posição permite que as variáveis de ambiente sejam lidas em runtime para substituir a configuração definida por segredos do usuário e arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-690">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="2fd71-691">Para fornecer a configuração de aplicativo de variáveis de ambiente adicionais, chame os provedores adicionais do aplicativo no `ConfigureAppConfiguration` e chame `AddEnvironmentVariables` com o prefixo:</span><span class="sxs-lookup"><span data-stu-id="2fd71-691">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="2fd71-692">Chame `AddEnvironmentVariables` Last para permitir que as variáveis de ambiente com o prefixo fornecido substituam valores de outros provedores.</span><span class="sxs-lookup"><span data-stu-id="2fd71-692">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="2fd71-693">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="2fd71-693">**Example**</span></span>

<span data-ttu-id="2fd71-694">O aplicativo de exemplo aproveita o método de conveniência estático `CreateDefaultBuilder` para criar o host, que inclui uma chamada para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-694">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="2fd71-695">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-695">Run the sample app.</span></span> <span data-ttu-id="2fd71-696">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-696">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2fd71-697">Observe que a saída contém o par chave-valor da variável de ambiente `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-697">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="2fd71-698">O valor reflete o ambiente no qual o aplicativo está em execução, normalmente `Development` ao executar localmente.</span><span class="sxs-lookup"><span data-stu-id="2fd71-698">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="2fd71-699">Para encurtar a lista de variáveis de ambiente renderizadas pelo aplicativo, o aplicativo filtra as variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2fd71-699">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="2fd71-700">Confira o arquivo *Pages/Index.cshtml.cs* do aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-700">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="2fd71-701">Para expor todas as variáveis de ambiente disponíveis para o aplicativo, altere o `FilteredConfiguration` em *pages/index. cshtml. cs* para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="2fd71-701">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="2fd71-702">Prefixos</span><span class="sxs-lookup"><span data-stu-id="2fd71-702">Prefixes</span></span>

<span data-ttu-id="2fd71-703">As variáveis de ambiente carregadas na configuração do aplicativo são filtradas ao fornecer um prefixo para o `AddEnvironmentVariables` método.</span><span class="sxs-lookup"><span data-stu-id="2fd71-703">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="2fd71-704">Por exemplo, para filtrar as variáveis de ambiente no prefixo `CUSTOM_`, forneça o prefixo para o provedor de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-704">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="2fd71-705">O prefixo é removido na criação dos pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-705">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="2fd71-706">Quando o construtor de host é criado, a configuração do host é fornecida por variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2fd71-706">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="2fd71-707">Para obter mais informações sobre o prefixo usado para essas variáveis de ambiente, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2fd71-707">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2fd71-708">**Prefixos de cadeia de conexão**</span><span class="sxs-lookup"><span data-stu-id="2fd71-708">**Connection string prefixes**</span></span>

<span data-ttu-id="2fd71-709">A API de configuração tem regras de processamento especiais para quatro variáveis de ambiente de cadeia de conexão envolvidas na configuração de cadeias de conexão do Azure para o ambiente de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-709">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="2fd71-710">As variáveis de ambiente com os prefixos mostrados na tabela são carregadas no aplicativo se nenhum prefixo for fornecido para `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-710">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="2fd71-711">Prefixo da cadeia de conexão</span><span class="sxs-lookup"><span data-stu-id="2fd71-711">Connection string prefix</span></span> | <span data-ttu-id="2fd71-712">Provedor</span><span class="sxs-lookup"><span data-stu-id="2fd71-712">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="2fd71-713">Provedor personalizado</span><span class="sxs-lookup"><span data-stu-id="2fd71-713">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="2fd71-714">MySQL</span><span class="sxs-lookup"><span data-stu-id="2fd71-714">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="2fd71-715">Banco de Dados SQL do Azure</span><span class="sxs-lookup"><span data-stu-id="2fd71-715">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="2fd71-716">SQL Server</span><span class="sxs-lookup"><span data-stu-id="2fd71-716">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="2fd71-717">Quando uma variável de ambiente for descoberta e carregada na configuração com qualquer um dos quatro prefixos mostrados na tabela:</span><span class="sxs-lookup"><span data-stu-id="2fd71-717">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="2fd71-718">A chave de configuração é criada removendo o prefixo da variável de ambiente e adicionando uma seção de chave de configuração (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="2fd71-718">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="2fd71-719">Um novo par chave-valor de configuração é criado para representar o provedor de conexão de banco de dados (exceto para `CUSTOMCONNSTR_`, que não tem um provedor indicado).</span><span class="sxs-lookup"><span data-stu-id="2fd71-719">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="2fd71-720">Chave de variável de ambiente</span><span class="sxs-lookup"><span data-stu-id="2fd71-720">Environment variable key</span></span> | <span data-ttu-id="2fd71-721">Chave de configuração convertida</span><span class="sxs-lookup"><span data-stu-id="2fd71-721">Converted configuration key</span></span> | <span data-ttu-id="2fd71-722">Entrada de configuração do provedor</span><span class="sxs-lookup"><span data-stu-id="2fd71-722">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2fd71-723">Entrada de configuração não criada.</span><span class="sxs-lookup"><span data-stu-id="2fd71-723">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2fd71-724">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2fd71-724">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2fd71-725">Valor: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="2fd71-725">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2fd71-726">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2fd71-726">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2fd71-727">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2fd71-727">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="2fd71-728">Chave: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="2fd71-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="2fd71-729">Valor: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="2fd71-729">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="2fd71-730">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="2fd71-730">**Example**</span></span>

<span data-ttu-id="2fd71-731">Uma variável de ambiente de cadeia de conexão personalizada é criada no servidor:</span><span class="sxs-lookup"><span data-stu-id="2fd71-731">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="2fd71-732">Nome: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="2fd71-732">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="2fd71-733">Valor: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="2fd71-733">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="2fd71-734">Se `IConfiguration` for injetado e atribuído a um campo chamado `_config` , leia o valor:</span><span class="sxs-lookup"><span data-stu-id="2fd71-734">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="2fd71-735">Provedor de Configuração de Arquivo</span><span class="sxs-lookup"><span data-stu-id="2fd71-735">File Configuration Provider</span></span>

<span data-ttu-id="2fd71-736"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> é a classe base para carregamento da configuração do sistema de arquivos.</span><span class="sxs-lookup"><span data-stu-id="2fd71-736"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="2fd71-737">Os provedores de configuração a seguir são dedicados a tipos específicos de arquivos:</span><span class="sxs-lookup"><span data-stu-id="2fd71-737">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="2fd71-738">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="2fd71-738">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="2fd71-739">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="2fd71-739">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="2fd71-740">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="2fd71-740">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="2fd71-741">Provedor de Configuração INI</span><span class="sxs-lookup"><span data-stu-id="2fd71-741">INI Configuration Provider</span></span>

<span data-ttu-id="2fd71-742">O <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> carrega a configuração de pares chave-valor do arquivo INI em runtime.</span><span class="sxs-lookup"><span data-stu-id="2fd71-742">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="2fd71-743">Para ativar a configuração de arquivo INI, chame o método de extensão <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-743">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2fd71-744">Os dois-pontos podem ser usados como um delimitador de seção na configuração do arquivo INI.</span><span class="sxs-lookup"><span data-stu-id="2fd71-744">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="2fd71-745">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="2fd71-745">Overloads permit specifying:</span></span>

* <span data-ttu-id="2fd71-746">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="2fd71-746">Whether the file is optional.</span></span>
* <span data-ttu-id="2fd71-747">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-747">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2fd71-748">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-748">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2fd71-749">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2fd71-749">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2fd71-750">Um exemplo genérico de um arquivo de configuração INI:</span><span class="sxs-lookup"><span data-stu-id="2fd71-750">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="2fd71-751">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="2fd71-751">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2fd71-752">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2fd71-752">section0:key0</span></span>
* <span data-ttu-id="2fd71-753">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2fd71-753">section0:key1</span></span>
* <span data-ttu-id="2fd71-754">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="2fd71-754">section1:subsection:key</span></span>
* <span data-ttu-id="2fd71-755">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="2fd71-755">section2:subsection0:key</span></span>
* <span data-ttu-id="2fd71-756">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="2fd71-756">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="2fd71-757">Provedor de Configuração JSON</span><span class="sxs-lookup"><span data-stu-id="2fd71-757">JSON Configuration Provider</span></span>

<span data-ttu-id="2fd71-758">O <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> carrega a configuração de pares chave-valor do arquivo JSON em runtime.</span><span class="sxs-lookup"><span data-stu-id="2fd71-758">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="2fd71-759">Para ativar a configuração de arquivo JSON, chame o método de extensão <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-759">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2fd71-760">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="2fd71-760">Overloads permit specifying:</span></span>

* <span data-ttu-id="2fd71-761">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="2fd71-761">Whether the file is optional.</span></span>
* <span data-ttu-id="2fd71-762">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-762">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2fd71-763">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-763">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2fd71-764">`AddJsonFile`é chamado automaticamente duas vezes quando um novo Construtor de hosts é inicializado com o `CreateDefaultBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2fd71-764">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="2fd71-765">O método é chamado para carregar a configuração de:</span><span class="sxs-lookup"><span data-stu-id="2fd71-765">The method is called to load configuration from:</span></span>

* <span data-ttu-id="2fd71-766">*appsettings.jsem*: este arquivo é lido primeiro.</span><span class="sxs-lookup"><span data-stu-id="2fd71-766">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="2fd71-767">A versão do ambiente do arquivo pode substituir os valores fornecidos pelo arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-767">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="2fd71-768">*appSettings. {Environment}. JSON*: a versão do ambiente do arquivo é carregada com base no [IHostingEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="2fd71-768">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="2fd71-769">Para obter mais informações, confira a seção [Configuração padrão](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="2fd71-769">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="2fd71-770">`CreateDefaultBuilder` também carrega:</span><span class="sxs-lookup"><span data-stu-id="2fd71-770">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="2fd71-771">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2fd71-771">Environment variables.</span></span>
* <span data-ttu-id="2fd71-772">[Segredos do usuário (Gerenciador de segredo)](xref:security/app-secrets) no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2fd71-772">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="2fd71-773">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="2fd71-773">Command-line arguments.</span></span>

<span data-ttu-id="2fd71-774">O Provedor de Configuração JSON é estabelecido primeiro.</span><span class="sxs-lookup"><span data-stu-id="2fd71-774">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="2fd71-775">Portanto, os segredos do usuário, as variáveis de ambiente e os argumentos de linha de comando substituem a configuração definida pelos arquivos *appsettings*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-775">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="2fd71-776">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo para arquivos que não sejam *appsettings.json* e *appsettings.{Environment}.json*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-776">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2fd71-777">**Exemplo**</span><span class="sxs-lookup"><span data-stu-id="2fd71-777">**Example**</span></span>

<span data-ttu-id="2fd71-778">O aplicativo de exemplo aproveita o método de conveniência estática `CreateDefaultBuilder` para criar o host, que inclui duas chamadas para `AddJsonFile` :</span><span class="sxs-lookup"><span data-stu-id="2fd71-778">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="2fd71-779">A primeira chamada para `AddJsonFile` carrega a configuração de *appsettings.jsem*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-779">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="2fd71-780">A segunda chamada para `AddJsonFile` carrega a configuração de *appSettings. { Ambiente}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-780">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="2fd71-781">Para *appsettings.Development.js* no aplicativo de exemplo, o seguinte arquivo é carregado:</span><span class="sxs-lookup"><span data-stu-id="2fd71-781">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="2fd71-782">Execute o aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-782">Run the sample app.</span></span> <span data-ttu-id="2fd71-783">Abra um navegador para o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-783">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="2fd71-784">A saída contém pares chave-valor para a configuração com base no ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-784">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="2fd71-785">O nível de log para a chave `Logging:LogLevel:Default` é `Debug` ao executar o aplicativo no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="2fd71-785">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="2fd71-786">Execute o aplicativo de exemplo novamente no ambiente de produção:</span><span class="sxs-lookup"><span data-stu-id="2fd71-786">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="2fd71-787">Abra as *Propriedades/launchSettings.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-787">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="2fd71-788">No `ConfigurationSample` perfil, altere o valor da variável de `ASPNETCORE_ENVIRONMENT` ambiente para `Production` .</span><span class="sxs-lookup"><span data-stu-id="2fd71-788">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="2fd71-789">Salve o arquivo e execute o aplicativo com o `dotnet run` em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="2fd71-789">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="2fd71-790">As configurações no *appsettings.Development.js* não substituem mais as configurações no *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="2fd71-790">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="2fd71-791">O nível de log para a chave `Logging:LogLevel:Default` é `Warning` .</span><span class="sxs-lookup"><span data-stu-id="2fd71-791">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="2fd71-792">Provedor de Configuração XML</span><span class="sxs-lookup"><span data-stu-id="2fd71-792">XML Configuration Provider</span></span>

<span data-ttu-id="2fd71-793">O <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> carrega a configuração de pares chave-valor do arquivo XML em runtime.</span><span class="sxs-lookup"><span data-stu-id="2fd71-793">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="2fd71-794">Para ativar a configuração de arquivo XML, chame o método de extensão <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-794">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2fd71-795">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="2fd71-795">Overloads permit specifying:</span></span>

* <span data-ttu-id="2fd71-796">Se o arquivo é opcional.</span><span class="sxs-lookup"><span data-stu-id="2fd71-796">Whether the file is optional.</span></span>
* <span data-ttu-id="2fd71-797">Se a configuração será recarregada caso o arquivo seja alterado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-797">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="2fd71-798">O <xref:Microsoft.Extensions.FileProviders.IFileProvider> usado para acessar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-798">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="2fd71-799">O nó raiz do arquivo de configuração é ignorado quando os pares chave-valor da configuração são criados.</span><span class="sxs-lookup"><span data-stu-id="2fd71-799">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="2fd71-800">Não especifique um DTD (definição de tipo de documento) ou um namespace no arquivo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-800">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="2fd71-801">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2fd71-801">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="2fd71-802">Os arquivos de configuração XML podem usar nomes de elemento distintos para seções repetidas:</span><span class="sxs-lookup"><span data-stu-id="2fd71-802">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="2fd71-803">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="2fd71-803">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2fd71-804">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2fd71-804">section0:key0</span></span>
* <span data-ttu-id="2fd71-805">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2fd71-805">section0:key1</span></span>
* <span data-ttu-id="2fd71-806">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2fd71-806">section1:key0</span></span>
* <span data-ttu-id="2fd71-807">section1:key1</span><span class="sxs-lookup"><span data-stu-id="2fd71-807">section1:key1</span></span>

<span data-ttu-id="2fd71-808">Elementos repetidos que usam o mesmo nome de elemento funcionarão se o atributo `name` for usado para diferenciar os elementos:</span><span class="sxs-lookup"><span data-stu-id="2fd71-808">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="2fd71-809">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="2fd71-809">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2fd71-810">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="2fd71-810">section:section0:key:key0</span></span>
* <span data-ttu-id="2fd71-811">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="2fd71-811">section:section0:key:key1</span></span>
* <span data-ttu-id="2fd71-812">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="2fd71-812">section:section1:key:key0</span></span>
* <span data-ttu-id="2fd71-813">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="2fd71-813">section:section1:key:key1</span></span>

<span data-ttu-id="2fd71-814">É possível usar atributos para fornecer valores:</span><span class="sxs-lookup"><span data-stu-id="2fd71-814">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="2fd71-815">O arquivo de configuração anterior carrega as seguintes chaves com `value`:</span><span class="sxs-lookup"><span data-stu-id="2fd71-815">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="2fd71-816">key:attribute</span><span class="sxs-lookup"><span data-stu-id="2fd71-816">key:attribute</span></span>
* <span data-ttu-id="2fd71-817">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="2fd71-817">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="2fd71-818">Provedor de Configuração de Chave por Arquivo</span><span class="sxs-lookup"><span data-stu-id="2fd71-818">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="2fd71-819">O <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> usa arquivos do diretório como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-819">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="2fd71-820">A chave é o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-820">The key is the file name.</span></span> <span data-ttu-id="2fd71-821">O valor contém o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-821">The value contains the file's contents.</span></span> <span data-ttu-id="2fd71-822">O Provedor de Configuração de Chave por arquivo é usado em cenários de hospedagem do Docker.</span><span class="sxs-lookup"><span data-stu-id="2fd71-822">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="2fd71-823">Para ativar a configuração de chave por arquivo, chame o método de extensão <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-823">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="2fd71-824">O `directoryPath` para os arquivos deve ser um caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="2fd71-824">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="2fd71-825">As sobrecargas permitem especificar:</span><span class="sxs-lookup"><span data-stu-id="2fd71-825">Overloads permit specifying:</span></span>

* <span data-ttu-id="2fd71-826">Um delegado `Action<KeyPerFileConfigurationSource>` que configura a origem.</span><span class="sxs-lookup"><span data-stu-id="2fd71-826">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="2fd71-827">Se o diretório é opcional, e o caminho para o diretório.</span><span class="sxs-lookup"><span data-stu-id="2fd71-827">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="2fd71-828">O sublinhado duplo (`__`) é usado como um delimitador de chave de configuração em nomes de arquivo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-828">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="2fd71-829">Por exemplo, o nome do arquivo `Logging__LogLevel__System` produz a chave de configuração `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-829">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="2fd71-830">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="2fd71-830">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="2fd71-831">Provedor de Configuração de Memória</span><span class="sxs-lookup"><span data-stu-id="2fd71-831">Memory Configuration Provider</span></span>

<span data-ttu-id="2fd71-832">O <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> usa uma coleção na memória como pares chave-valor de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-832">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="2fd71-833">Para ativar a configuração de coleção na memória, chame o método de extensão <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> em uma instância do <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-833">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="2fd71-834">O provedor de configuração pode ser inicializado com um `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-834">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="2fd71-835">Chame `ConfigureAppConfiguration` ao criar o host para especificar a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-835">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="2fd71-836">No exemplo a seguir, um dicionário de configuração é criado:</span><span class="sxs-lookup"><span data-stu-id="2fd71-836">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="2fd71-837">O dicionário é usado com uma chamada para `AddInMemoryCollection` a fim de fornecer a configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-837">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="2fd71-838">GetValue</span><span class="sxs-lookup"><span data-stu-id="2fd71-838">GetValue</span></span>

<span data-ttu-id="2fd71-839">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)extrai um único valor da configuração com uma chave especificada e converte-o para o tipo de não coleção especificado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-839">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="2fd71-840">Uma sobrecarga aceita um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="2fd71-840">An overload accepts a default value.</span></span>

<span data-ttu-id="2fd71-841">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="2fd71-841">The following example:</span></span>

* <span data-ttu-id="2fd71-842">extrai o valor de cadeia de caracteres da configuração com a chave `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-842">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="2fd71-843">Se `NumberKey` não for encontrado nas chaves de configuração, será usado o valor padrão `99`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-843">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="2fd71-844">Digita o valor como um `int`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-844">Types the value as an `int`.</span></span>
* <span data-ttu-id="2fd71-845">Armazena o valor na propriedade `NumberConfig` para uso pela página.</span><span class="sxs-lookup"><span data-stu-id="2fd71-845">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="2fd71-846">GetSection, GetChildren e Exists</span><span class="sxs-lookup"><span data-stu-id="2fd71-846">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="2fd71-847">Para os exemplos a seguir, considere o seguinte arquivo JSON.</span><span class="sxs-lookup"><span data-stu-id="2fd71-847">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="2fd71-848">Há quatro chaves em duas seções, cada uma delas inclui um par de subseções:</span><span class="sxs-lookup"><span data-stu-id="2fd71-848">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="2fd71-849">Quando o arquivo é lido na configuração, as seguintes chaves hierárquicas exclusivas são criadas para conter os valores de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-849">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="2fd71-850">section0:key0</span><span class="sxs-lookup"><span data-stu-id="2fd71-850">section0:key0</span></span>
* <span data-ttu-id="2fd71-851">section0:key1</span><span class="sxs-lookup"><span data-stu-id="2fd71-851">section0:key1</span></span>
* <span data-ttu-id="2fd71-852">section1:key0</span><span class="sxs-lookup"><span data-stu-id="2fd71-852">section1:key0</span></span>
* <span data-ttu-id="2fd71-853">section1:key1</span><span class="sxs-lookup"><span data-stu-id="2fd71-853">section1:key1</span></span>
* <span data-ttu-id="2fd71-854">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="2fd71-854">section2:subsection0:key0</span></span>
* <span data-ttu-id="2fd71-855">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="2fd71-855">section2:subsection0:key1</span></span>
* <span data-ttu-id="2fd71-856">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="2fd71-856">section2:subsection1:key0</span></span>
* <span data-ttu-id="2fd71-857">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="2fd71-857">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="2fd71-858">GetSection</span><span class="sxs-lookup"><span data-stu-id="2fd71-858">GetSection</span></span>

<span data-ttu-id="2fd71-859">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrai uma subseção de configuração com a chave de subseção especificada.</span><span class="sxs-lookup"><span data-stu-id="2fd71-859">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="2fd71-860">Para retornar um <xref:Microsoft.Extensions.Configuration.IConfigurationSection> que contenha apenas os pares chave-valor em `section1`, chame `GetSection` e forneça o nome da seção:</span><span class="sxs-lookup"><span data-stu-id="2fd71-860">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="2fd71-861">`configSection` não tem um valor, apenas uma chave e um caminho.</span><span class="sxs-lookup"><span data-stu-id="2fd71-861">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="2fd71-862">Da mesma forma, para obter os valores de chaves em `section2:subsection0`, chame `GetSection` e forneça o caminho de seção:</span><span class="sxs-lookup"><span data-stu-id="2fd71-862">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="2fd71-863">`GetSection` nunca retorna `null`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-863">`GetSection` never returns `null`.</span></span> <span data-ttu-id="2fd71-864">Se uma seção correspondente não for encontrada, um `IConfigurationSection` vazio será retornado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-864">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="2fd71-865">Quando `GetSection` retorna uma seção correspondente, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> não é preenchido.</span><span class="sxs-lookup"><span data-stu-id="2fd71-865">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="2fd71-866"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> e <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> são retornados quando a seção existe.</span><span class="sxs-lookup"><span data-stu-id="2fd71-866">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="2fd71-867">GetChildren</span><span class="sxs-lookup"><span data-stu-id="2fd71-867">GetChildren</span></span>

<span data-ttu-id="2fd71-868">Uma chamada para [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) em `section2` obtém um `IEnumerable<IConfigurationSection>` que inclui:</span><span class="sxs-lookup"><span data-stu-id="2fd71-868">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="2fd71-869">Exists</span><span class="sxs-lookup"><span data-stu-id="2fd71-869">Exists</span></span>

<span data-ttu-id="2fd71-870">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) para determinar se há uma seção de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-870">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="2fd71-871">Considerando os dados de exemplo, `sectionExists` é `false`, pois não existe uma seção `section2:subsection2` nos dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-871">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="2fd71-872">Associar a um gráfico de objeto</span><span class="sxs-lookup"><span data-stu-id="2fd71-872">Bind to an object graph</span></span>

<span data-ttu-id="2fd71-873"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> é capaz de associar um grafo de objeto POCO inteiro.</span><span class="sxs-lookup"><span data-stu-id="2fd71-873"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="2fd71-874">Assim como acontece com a associação de um objeto simples, somente as propriedades públicas de leitura/gravação são associadas.</span><span class="sxs-lookup"><span data-stu-id="2fd71-874">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="2fd71-875">O exemplo contém um modelo `TvShow` cujo grafo do objeto inclui as classes `Metadata` e `Actors` (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="2fd71-875">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="2fd71-876">O aplicativo de exemplo tem um arquivo *tvshow.xml* que contém os dados de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-876">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="2fd71-877">A configuração está associada ao método do grafo de objeto `TvShow` inteiro com o método `Bind`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-877">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="2fd71-878">A instância associada é atribuída a uma propriedade para renderização:</span><span class="sxs-lookup"><span data-stu-id="2fd71-878">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="2fd71-879">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)associa e retorna o tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-879">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="2fd71-880">O `Get<T>` é mais conveniente do que usar `Bind`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-880">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="2fd71-881">O código a seguir mostra como usar `Get<T>` o com o exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="2fd71-881">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="2fd71-882">Associar uma matriz a uma classe</span><span class="sxs-lookup"><span data-stu-id="2fd71-882">Bind an array to a class</span></span>

<span data-ttu-id="2fd71-883">*O aplicativo de exemplo demonstra os conceitos explicados nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="2fd71-883">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="2fd71-884">O <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> dá suporte a matrizes de associação para objetos usando os índices em chaves de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-884">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="2fd71-885">Qualquer formato de matriz que expõe um segmento de chave numérico ( `:0:` , `:1:` , &hellip; `:{n}:` ) é capaz de associar a matriz a uma matriz de classe poco.</span><span class="sxs-lookup"><span data-stu-id="2fd71-885">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="2fd71-886">A associação é fornecida por convenção.</span><span class="sxs-lookup"><span data-stu-id="2fd71-886">Binding is provided by convention.</span></span> <span data-ttu-id="2fd71-887">Provedores de configuração personalizados não são necessários para implementar a associação de matriz.</span><span class="sxs-lookup"><span data-stu-id="2fd71-887">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="2fd71-888">**Processamento de matriz na memória**</span><span class="sxs-lookup"><span data-stu-id="2fd71-888">**In-memory array processing**</span></span>

<span data-ttu-id="2fd71-889">Considere as chaves de configuração e os valores mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="2fd71-889">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="2fd71-890">Chave</span><span class="sxs-lookup"><span data-stu-id="2fd71-890">Key</span></span>             | <span data-ttu-id="2fd71-891">Valor</span><span class="sxs-lookup"><span data-stu-id="2fd71-891">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="2fd71-892">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="2fd71-892">array:entries:0</span></span> | <span data-ttu-id="2fd71-893">value0</span><span class="sxs-lookup"><span data-stu-id="2fd71-893">value0</span></span> |
| <span data-ttu-id="2fd71-894">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="2fd71-894">array:entries:1</span></span> | <span data-ttu-id="2fd71-895">value1</span><span class="sxs-lookup"><span data-stu-id="2fd71-895">value1</span></span> |
| <span data-ttu-id="2fd71-896">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="2fd71-896">array:entries:2</span></span> | <span data-ttu-id="2fd71-897">value2</span><span class="sxs-lookup"><span data-stu-id="2fd71-897">value2</span></span> |
| <span data-ttu-id="2fd71-898">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="2fd71-898">array:entries:4</span></span> | <span data-ttu-id="2fd71-899">value4</span><span class="sxs-lookup"><span data-stu-id="2fd71-899">value4</span></span> |
| <span data-ttu-id="2fd71-900">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="2fd71-900">array:entries:5</span></span> | <span data-ttu-id="2fd71-901">value5</span><span class="sxs-lookup"><span data-stu-id="2fd71-901">value5</span></span> |

<span data-ttu-id="2fd71-902">Essas chaves e valores são carregados no aplicativo de exemplo usando o Provedor de Configuração de Memória:</span><span class="sxs-lookup"><span data-stu-id="2fd71-902">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="2fd71-903">A matriz ignora um valor para o índice &num;3.</span><span class="sxs-lookup"><span data-stu-id="2fd71-903">The array skips a value for index &num;3.</span></span> <span data-ttu-id="2fd71-904">O associador de configuração não é capaz de associar valores nulos ou criar entradas nulas em objetos associados, o que fica claro em um momento quando o resultado da associação dessa matriz a um objeto é demonstrado.</span><span class="sxs-lookup"><span data-stu-id="2fd71-904">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="2fd71-905">No aplicativo de exemplo, uma classe POCO está disponível para armazenar os dados de configuração associados:</span><span class="sxs-lookup"><span data-stu-id="2fd71-905">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="2fd71-906">Os dados de configuração estão associados ao objeto:</span><span class="sxs-lookup"><span data-stu-id="2fd71-906">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="2fd71-907">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)a sintaxe também pode ser usada, o que resulta em um código mais compacto:</span><span class="sxs-lookup"><span data-stu-id="2fd71-907">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="2fd71-908">O objeto associado, uma instância de `ArrayExample`, recebe os dados da matriz de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-908">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="2fd71-909">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="2fd71-909">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="2fd71-910">`ArrayExample.Entries` Valor</span><span class="sxs-lookup"><span data-stu-id="2fd71-910">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="2fd71-911">0</span><span class="sxs-lookup"><span data-stu-id="2fd71-911">0</span></span>                            | <span data-ttu-id="2fd71-912">value0</span><span class="sxs-lookup"><span data-stu-id="2fd71-912">value0</span></span>                       |
| <span data-ttu-id="2fd71-913">1</span><span class="sxs-lookup"><span data-stu-id="2fd71-913">1</span></span>                            | <span data-ttu-id="2fd71-914">value1</span><span class="sxs-lookup"><span data-stu-id="2fd71-914">value1</span></span>                       |
| <span data-ttu-id="2fd71-915">2</span><span class="sxs-lookup"><span data-stu-id="2fd71-915">2</span></span>                            | <span data-ttu-id="2fd71-916">value2</span><span class="sxs-lookup"><span data-stu-id="2fd71-916">value2</span></span>                       |
| <span data-ttu-id="2fd71-917">3</span><span class="sxs-lookup"><span data-stu-id="2fd71-917">3</span></span>                            | <span data-ttu-id="2fd71-918">value4</span><span class="sxs-lookup"><span data-stu-id="2fd71-918">value4</span></span>                       |
| <span data-ttu-id="2fd71-919">4</span><span class="sxs-lookup"><span data-stu-id="2fd71-919">4</span></span>                            | <span data-ttu-id="2fd71-920">value5</span><span class="sxs-lookup"><span data-stu-id="2fd71-920">value5</span></span>                       |

<span data-ttu-id="2fd71-921">O índice &num;3 no objeto associado contém os dados de configuração para a chave de configuração `array:4` e seu valor de `value4`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-921">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="2fd71-922">Quando os dados de configuração que contêm uma matriz são associados, os índices da matriz nas chaves de configuração são simplesmente usados para iterar os dados de configuração ao criar o objeto.</span><span class="sxs-lookup"><span data-stu-id="2fd71-922">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="2fd71-923">Um valor nulo não pode ser mantido nos dados de configuração, e uma entrada de valor nulo não será criada em um objeto de associação quando uma matriz nas chaves de configuração ignorar um ou mais índices.</span><span class="sxs-lookup"><span data-stu-id="2fd71-923">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="2fd71-924">O item de configuração ausente para o índice &num;3 pode ser fornecido antes da associação à instância `ArrayExample` por qualquer provedor de configuração que produza o par chave-valor correto na configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-924">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="2fd71-925">Se o exemplo incluir um Provedor de Configuração JSON adicional com o par chave-valor ausente, o `ArrayExample.Entries` coincidirá com a matriz de configuração completa:</span><span class="sxs-lookup"><span data-stu-id="2fd71-925">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="2fd71-926">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-926">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="2fd71-927">Em `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="2fd71-927">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="2fd71-928">O par chave-valor mostrado na tabela é carregado na configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-928">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="2fd71-929">Chave</span><span class="sxs-lookup"><span data-stu-id="2fd71-929">Key</span></span>             | <span data-ttu-id="2fd71-930">Valor</span><span class="sxs-lookup"><span data-stu-id="2fd71-930">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="2fd71-931">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="2fd71-931">array:entries:3</span></span> | <span data-ttu-id="2fd71-932">value3</span><span class="sxs-lookup"><span data-stu-id="2fd71-932">value3</span></span> |

<span data-ttu-id="2fd71-933">Se a instância da classe `ArrayExample` for associada após o Provedor de Configuração JSON incluir a entrada para o índice &num;3, a matriz `ArrayExample.Entries` incluirá o valor.</span><span class="sxs-lookup"><span data-stu-id="2fd71-933">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="2fd71-934">Índice `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="2fd71-934">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="2fd71-935">`ArrayExample.Entries` Valor</span><span class="sxs-lookup"><span data-stu-id="2fd71-935">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="2fd71-936">0</span><span class="sxs-lookup"><span data-stu-id="2fd71-936">0</span></span>                            | <span data-ttu-id="2fd71-937">value0</span><span class="sxs-lookup"><span data-stu-id="2fd71-937">value0</span></span>                       |
| <span data-ttu-id="2fd71-938">1</span><span class="sxs-lookup"><span data-stu-id="2fd71-938">1</span></span>                            | <span data-ttu-id="2fd71-939">value1</span><span class="sxs-lookup"><span data-stu-id="2fd71-939">value1</span></span>                       |
| <span data-ttu-id="2fd71-940">2</span><span class="sxs-lookup"><span data-stu-id="2fd71-940">2</span></span>                            | <span data-ttu-id="2fd71-941">value2</span><span class="sxs-lookup"><span data-stu-id="2fd71-941">value2</span></span>                       |
| <span data-ttu-id="2fd71-942">3</span><span class="sxs-lookup"><span data-stu-id="2fd71-942">3</span></span>                            | <span data-ttu-id="2fd71-943">value3</span><span class="sxs-lookup"><span data-stu-id="2fd71-943">value3</span></span>                       |
| <span data-ttu-id="2fd71-944">4</span><span class="sxs-lookup"><span data-stu-id="2fd71-944">4</span></span>                            | <span data-ttu-id="2fd71-945">value4</span><span class="sxs-lookup"><span data-stu-id="2fd71-945">value4</span></span>                       |
| <span data-ttu-id="2fd71-946">5</span><span class="sxs-lookup"><span data-stu-id="2fd71-946">5</span></span>                            | <span data-ttu-id="2fd71-947">value5</span><span class="sxs-lookup"><span data-stu-id="2fd71-947">value5</span></span>                       |

<span data-ttu-id="2fd71-948">**Processamento de matriz JSON**</span><span class="sxs-lookup"><span data-stu-id="2fd71-948">**JSON array processing**</span></span>

<span data-ttu-id="2fd71-949">Se um arquivo JSON contiver uma matriz, as chaves de configuração serão criadas para os elementos da matriz com um índice de seção de base zero.</span><span class="sxs-lookup"><span data-stu-id="2fd71-949">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="2fd71-950">No arquivo de configuração a seguir, `subsection` é uma matriz:</span><span class="sxs-lookup"><span data-stu-id="2fd71-950">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="2fd71-951">O Provedor de Configuração JSON lê os dados de configuração para os seguintes pares chave-valor:</span><span class="sxs-lookup"><span data-stu-id="2fd71-951">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="2fd71-952">Chave</span><span class="sxs-lookup"><span data-stu-id="2fd71-952">Key</span></span>                     | <span data-ttu-id="2fd71-953">Valor</span><span class="sxs-lookup"><span data-stu-id="2fd71-953">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="2fd71-954">json_array:key</span><span class="sxs-lookup"><span data-stu-id="2fd71-954">json_array:key</span></span>          | <span data-ttu-id="2fd71-955">valueA</span><span class="sxs-lookup"><span data-stu-id="2fd71-955">valueA</span></span> |
| <span data-ttu-id="2fd71-956">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="2fd71-956">json_array:subsection:0</span></span> | <span data-ttu-id="2fd71-957">valueB</span><span class="sxs-lookup"><span data-stu-id="2fd71-957">valueB</span></span> |
| <span data-ttu-id="2fd71-958">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="2fd71-958">json_array:subsection:1</span></span> | <span data-ttu-id="2fd71-959">valueC</span><span class="sxs-lookup"><span data-stu-id="2fd71-959">valueC</span></span> |
| <span data-ttu-id="2fd71-960">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="2fd71-960">json_array:subsection:2</span></span> | <span data-ttu-id="2fd71-961">valueD</span><span class="sxs-lookup"><span data-stu-id="2fd71-961">valueD</span></span> |

<span data-ttu-id="2fd71-962">No aplicativo de exemplo, a seguinte classe POCO está disponível para associar os pares chave-valor de configuração:</span><span class="sxs-lookup"><span data-stu-id="2fd71-962">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="2fd71-963">Após a associação, `JsonArrayExample.Key` contém o valor `valueA`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-963">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="2fd71-964">Os valores de subseção são armazenados na propriedade matriz POCO, `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-964">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="2fd71-965">Índice `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="2fd71-965">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="2fd71-966">`JsonArrayExample.Subsection` Valor</span><span class="sxs-lookup"><span data-stu-id="2fd71-966">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="2fd71-967">0</span><span class="sxs-lookup"><span data-stu-id="2fd71-967">0</span></span>                                   | <span data-ttu-id="2fd71-968">valueB</span><span class="sxs-lookup"><span data-stu-id="2fd71-968">valueB</span></span>                              |
| <span data-ttu-id="2fd71-969">1</span><span class="sxs-lookup"><span data-stu-id="2fd71-969">1</span></span>                                   | <span data-ttu-id="2fd71-970">valueC</span><span class="sxs-lookup"><span data-stu-id="2fd71-970">valueC</span></span>                              |
| <span data-ttu-id="2fd71-971">2</span><span class="sxs-lookup"><span data-stu-id="2fd71-971">2</span></span>                                   | <span data-ttu-id="2fd71-972">valueD</span><span class="sxs-lookup"><span data-stu-id="2fd71-972">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="2fd71-973">Provedor de Configuração personalizado</span><span class="sxs-lookup"><span data-stu-id="2fd71-973">Custom configuration provider</span></span>

<span data-ttu-id="2fd71-974">O aplicativo de exemplo demonstra como criar um provedor de configuração básico que lê os pares chave-valor da configuração de um banco de dados usando [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="2fd71-974">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="2fd71-975">O provedor tem as seguintes características:</span><span class="sxs-lookup"><span data-stu-id="2fd71-975">The provider has the following characteristics:</span></span>

* <span data-ttu-id="2fd71-976">O banco de dados EF na memória é usado para fins de demonstração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-976">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="2fd71-977">Para usar um banco de dados que exija uma cadeia de conexão, implemente um `ConfigurationBuilder` secundário para fornecer a cadeia de conexão de outro provedor de configuração.</span><span class="sxs-lookup"><span data-stu-id="2fd71-977">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="2fd71-978">O provedor lê uma tabela de banco de dados na configuração na inicialização.</span><span class="sxs-lookup"><span data-stu-id="2fd71-978">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="2fd71-979">O provedor não consulta o banco de dados em uma base por chave.</span><span class="sxs-lookup"><span data-stu-id="2fd71-979">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="2fd71-980">O recarregamento na alteração não está implementado, portanto, a atualização do banco de dados após a inicialização do aplicativo não tem efeito sobre a configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-980">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="2fd71-981">Defina uma entidade `EFConfigurationValue` para armazenar valores de configuração no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="2fd71-981">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="2fd71-982">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-982">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="2fd71-983">Adicione um `EFConfigurationContext` para armazenar e acessar os valores configurados.</span><span class="sxs-lookup"><span data-stu-id="2fd71-983">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="2fd71-984">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-984">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="2fd71-985">Crie uma classe que implementa <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-985">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="2fd71-986">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-986">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="2fd71-987">Crie o provedor de configuração personalizado através da herança de <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-987">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="2fd71-988">O provedor de configuração inicializa o banco de dados quando ele está vazio.</span><span class="sxs-lookup"><span data-stu-id="2fd71-988">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="2fd71-989">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-989">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="2fd71-990">Um método de extensão `AddEFConfiguration` permite adicionar a fonte de configuração a um `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-990">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="2fd71-991">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-991">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="2fd71-992">O código a seguir mostra como usar o `EFConfigurationProvider` personalizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fd71-992">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="2fd71-993">Acessar a configuração durante a inicialização</span><span class="sxs-lookup"><span data-stu-id="2fd71-993">Access configuration during startup</span></span>

<span data-ttu-id="2fd71-994">Injete `IConfiguration` no construtor `Startup` para acessar os valores de configuração em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2fd71-994">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2fd71-995">Para acessar a configuração em `Startup.Configure`, injete `IConfiguration` diretamente no método ou use a instância do construtor:</span><span class="sxs-lookup"><span data-stu-id="2fd71-995">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="2fd71-996">Para obter um exemplo de como acessar a configuração usando os métodos de conveniência de inicialização, consulte [Inicialização do aplicativo: métodos de conveniência](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="2fd71-996">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="2fd71-997">Acessar a configuração em uma Razor página de páginas ou exibição do MVC</span><span class="sxs-lookup"><span data-stu-id="2fd71-997">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="2fd71-998">Para acessar as definições de configuração em uma Razor página de páginas ou em uma exibição do MVC, adicione uma [diretiva using](xref:mvc/views/razor#using) ([referência C#: usando diretiva](/dotnet/csharp/language-reference/keywords/using-directive)) para o [namespaceMicrosoft.Extensions.Configuração](xref:Microsoft.Extensions.Configuration) e insira <xref:Microsoft.Extensions.Configuration.IConfiguration> na página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="2fd71-998">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="2fd71-999">Em uma Razor página de páginas:</span><span class="sxs-lookup"><span data-stu-id="2fd71-999">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="2fd71-1000">Em uma exibição do MVC:</span><span class="sxs-lookup"><span data-stu-id="2fd71-1000">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="2fd71-1001">Adicionar configuração de um assembly externo</span><span class="sxs-lookup"><span data-stu-id="2fd71-1001">Add configuration from an external assembly</span></span>

<span data-ttu-id="2fd71-1002">Uma implementação <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> permite adicionar melhorias a um aplicativo durante a inicialização de um assembly externo fora da classe `Startup` do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fd71-1002">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="2fd71-1003">Para obter mais informações, consulte <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2fd71-1003">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2fd71-1004">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2fd71-1004">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
