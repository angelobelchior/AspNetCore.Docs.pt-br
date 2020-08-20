---
title: Armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core
author: rick-anderson
description: Saiba como armazenar e recuperar informações confidenciais como segredos do aplicativo durante o desenvolvimento de um aplicativo ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
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
uid: security/app-secrets
ms.openlocfilehash: 74c9ae63ffbe39d6ba6e77aee8f6adcc8c8a157a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634898"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="cb8b2-103">Armazenamento seguro de segredos do aplicativo em desenvolvimento no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb8b2-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cb8b2-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="cb8b2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="cb8b2-105">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cb8b2-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cb8b2-106">Este documento explica técnicas para armazenar e recuperar dados confidenciais durante o desenvolvimento de um aplicativo de ASP.NET Core em um computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="cb8b2-107">Nunca armazene senhas ou outros dados confidenciais no código-fonte.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="cb8b2-108">Os segredos de produção não devem ser usados para desenvolvimento ou teste.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="cb8b2-109">Os segredos não devem ser implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="cb8b2-110">Em vez disso, os segredos devem ser disponibilizados no ambiente de produção por meio de um meio controlado, como variáveis de ambiente, Azure Key Vault, etc. Você pode armazenar e proteger os segredos de produção e de teste do Azure com o [provedor de configuração Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="cb8b2-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="cb8b2-111">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="cb8b2-111">Environment variables</span></span>

<span data-ttu-id="cb8b2-112">As variáveis de ambiente são usadas para evitar o armazenamento de segredos do aplicativo no código ou nos arquivos de configuração locais.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="cb8b2-113">Variáveis de ambiente substituem valores de configuração para todas as fontes de configuração especificadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="cb8b2-114">Considere um aplicativo Web ASP.NET Core no qual a segurança **das contas de usuário individuais** está habilitada.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="cb8b2-115">Uma cadeia de conexão de banco de dados padrão é incluída naappsettings.jsdo projeto \* no\* arquivo com a chave `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="cb8b2-116">A cadeia de conexão padrão é para o LocalDB, que é executado no modo de usuário e não requer uma senha.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="cb8b2-117">Durante a implantação do aplicativo, o `DefaultConnection` valor da chave pode ser substituído por um valor de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="cb8b2-118">A variável de ambiente pode armazenar a cadeia de conexão completa com credenciais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="cb8b2-119">Variáveis de ambiente geralmente são armazenadas em texto simples e não criptografado.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="cb8b2-120">Se o computador ou o processo estiver comprometido, as variáveis de ambiente poderão ser acessadas por partes não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="cb8b2-121">Outras medidas para evitar a divulgação de segredos do usuário podem ser necessárias.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="cb8b2-122">Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="cb8b2-122">Secret Manager</span></span>

<span data-ttu-id="cb8b2-123">A ferramenta Gerenciador de segredo armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="cb8b2-124">Nesse contexto, um elemento de dados confidenciais é um segredo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="cb8b2-125">Os segredos do aplicativo são armazenados em um local separado da árvore do projeto.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="cb8b2-126">Os segredos do aplicativo são associados a um projeto específico ou compartilhados entre vários projetos.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="cb8b2-127">Os segredos do aplicativo não são verificados no controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="cb8b2-128">A ferramenta Gerenciador de segredo não criptografa os segredos armazenados e não deve ser tratada como um repositório confiável.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="cb8b2-129">É apenas para fins de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-129">It's for development purposes only.</span></span> <span data-ttu-id="cb8b2-130">As chaves e os valores são armazenados em um arquivo de configuração JSON no diretório de perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="cb8b2-131">Como funciona a ferramenta Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="cb8b2-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="cb8b2-132">A ferramenta Gerenciador de segredo abstrai os detalhes da implementação, como onde e como os valores são armazenados.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="cb8b2-133">Você pode usar a ferramenta sem conhecer esses detalhes de implementação.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="cb8b2-134">Os valores são armazenados em um arquivo de configuração JSON em uma pasta de perfil de usuário protegida pelo sistema no computador local:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="cb8b2-135">Windows</span><span class="sxs-lookup"><span data-stu-id="cb8b2-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="cb8b2-136">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="cb8b2-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="cb8b2-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="cb8b2-138">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="cb8b2-139">Nos caminhos de arquivo anteriores, substitua `<user_secrets_id>` pelo `UserSecretsId` valor especificado no arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="cb8b2-140">Não escreva o código que depende do local ou do formato dos dados salvos com a ferramenta Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="cb8b2-141">Esses detalhes de implementação podem ser alterados.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-141">These implementation details may change.</span></span> <span data-ttu-id="cb8b2-142">Por exemplo, os valores secretos não são criptografados, mas podem estar no futuro.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="cb8b2-143">Habilitar o armazenamento secreto</span><span class="sxs-lookup"><span data-stu-id="cb8b2-143">Enable secret storage</span></span>

<span data-ttu-id="cb8b2-144">A ferramenta Gerenciador de segredo opera em definições de configuração específicas do projeto armazenadas no seu perfil de usuário.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="cb8b2-145">A ferramenta Gerenciador de segredo inclui um `init` comando no SDK do .NET Core 3.0.100 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="cb8b2-146">Para usar os segredos do usuário, execute o seguinte comando no diretório do projeto:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="cb8b2-147">O comando anterior adiciona um `UserSecretsId` elemento dentro de um `PropertyGroup` do arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="cb8b2-148">Por padrão, o texto interno de `UserSecretsId` é um GUID.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="cb8b2-149">O texto interno é arbitrário, mas é exclusivo para o projeto.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="cb8b2-150">No Visual Studio, clique com o botão direito do mouse no projeto em Gerenciador de Soluções e selecione **gerenciar segredos do usuário** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="cb8b2-151">Esse gesto adiciona um `UserSecretsId` elemento, populado com um GUID, ao arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="cb8b2-152">Definir um segredo</span><span class="sxs-lookup"><span data-stu-id="cb8b2-152">Set a secret</span></span>

<span data-ttu-id="cb8b2-153">Defina um segredo de aplicativo que consiste em uma chave e seu valor.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="cb8b2-154">O segredo é associado ao valor do projeto `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="cb8b2-155">Por exemplo, execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="cb8b2-156">No exemplo anterior, os dois pontos indica que `Movies` é um literal de objeto com uma `ServiceApiKey` propriedade.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="cb8b2-157">A ferramenta Gerenciador de segredo também pode ser usada em outros diretórios.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="cb8b2-158">Use a `--project` opção para fornecer o caminho do sistema de arquivos no qual o arquivo *. csproj* existe.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="cb8b2-159">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="cb8b2-160">Nivelamento de estrutura JSON no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb8b2-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="cb8b2-161">O gesto **gerenciar segredos do usuário** do Visual Studio abre uma *secrets.jsno* arquivo no editor de texto.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="cb8b2-162">Substitua o conteúdo de *secrets.js* com os pares chave-valor a serem armazenados.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="cb8b2-163">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="cb8b2-164">A estrutura JSON é achatada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="cb8b2-165">Por exemplo, `dotnet user-secrets remove "Movies:ConnectionString"` a execução recolhe o `Movies` literal do objeto.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="cb8b2-166">O arquivo modificado é semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="cb8b2-167">Definir vários segredos</span><span class="sxs-lookup"><span data-stu-id="cb8b2-167">Set multiple secrets</span></span>

<span data-ttu-id="cb8b2-168">Um lote de segredos pode ser definido por meio de tubulação JSON para o `set` comando.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="cb8b2-169">No exemplo a seguir, o *input.jsno* conteúdo do arquivo é canalizado para o `set` comando.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="cb8b2-170">Windows</span><span class="sxs-lookup"><span data-stu-id="cb8b2-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="cb8b2-171">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="cb8b2-172">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="cb8b2-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="cb8b2-173">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="cb8b2-174">Acessar um segredo</span><span class="sxs-lookup"><span data-stu-id="cb8b2-174">Access a secret</span></span>

<span data-ttu-id="cb8b2-175">A [API de configuração do ASP.NET Core](xref:fundamentals/configuration/index) fornece acesso aos segredos do Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="cb8b2-176">A fonte de configuração segredos do usuário é adicionada automaticamente no modo de desenvolvimento quando o projeto chama <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> para inicializar uma nova instância do host com padrões pré-configurados.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="cb8b2-177">`CreateDefaultBuilder` chama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando o <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> é <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="cb8b2-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="cb8b2-178">Quando `CreateDefaultBuilder` não for chamado, adicione a fonte de configuração de segredos do usuário explicitamente chamando <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="cb8b2-179">Chame `AddUserSecrets` somente quando o aplicativo for executado no ambiente de desenvolvimento, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="cb8b2-180">Os segredos do usuário podem ser recuperados por meio da `Configuration` API:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="cb8b2-181">Mapear segredos para um POCO</span><span class="sxs-lookup"><span data-stu-id="cb8b2-181">Map secrets to a POCO</span></span>

<span data-ttu-id="cb8b2-182">O mapeamento de um literal de objeto inteiro para um POCO (uma classe .NET simples com propriedades) é útil para agregar propriedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="cb8b2-183">Para mapear os segredos anteriores para um POCO, use o `Configuration` recurso de associação de grafo de [objeto](xref:fundamentals/configuration/index#bind-to-an-object-graph) da API.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="cb8b2-184">O código a seguir é associado a um `MovieSettings` poco personalizado e acessa o `ServiceApiKey` valor da propriedade:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="cb8b2-185">Os `Movies:ConnectionString` `Movies:ServiceApiKey` segredos e são mapeados para as respectivas propriedades no `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="cb8b2-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="cb8b2-186">Substituição de cadeia de caracteres com segredos</span><span class="sxs-lookup"><span data-stu-id="cb8b2-186">String replacement with secrets</span></span>

<span data-ttu-id="cb8b2-187">O armazenamento de senhas em texto sem formatação não é seguro.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="cb8b2-188">Por exemplo, uma cadeia de conexão de banco de dados armazenada no *appsettings.jsno* pode incluir uma senha para o usuário especificado:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="cb8b2-189">Uma abordagem mais segura é armazenar a senha como um segredo.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="cb8b2-190">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="cb8b2-191">Remova o `Password` par chave-valor da cadeia de conexão no *appsettings.jsem*.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="cb8b2-192">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="cb8b2-193">O valor do segredo pode ser definido na propriedade de um <xref:System.Data.SqlClient.SqlConnectionStringBuilder> objeto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> para concluir a cadeia de conexão:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="cb8b2-194">Listar os segredos</span><span class="sxs-lookup"><span data-stu-id="cb8b2-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="cb8b2-195">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="cb8b2-196">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="cb8b2-197">No exemplo anterior, dois-pontos nos nomes de chave denota a hierarquia de objeto dentro de *secrets.jsem*.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="cb8b2-198">Remover um único segredo</span><span class="sxs-lookup"><span data-stu-id="cb8b2-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="cb8b2-199">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="cb8b2-200">Osecrets.jsdo aplicativo \* no\* arquivo foi modificado para remover o par chave-valor associado à `MoviesConnectionString` chave:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="cb8b2-201">`dotnet user-secrets list` exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="cb8b2-202">Remover todos os segredos</span><span class="sxs-lookup"><span data-stu-id="cb8b2-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="cb8b2-203">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="cb8b2-204">Todos os segredos do usuário para o aplicativo foram excluídos do *secrets.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="cb8b2-205">`dotnet user-secrets list`A execução exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="cb8b2-206">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cb8b2-206">Additional resources</span></span>

* <span data-ttu-id="cb8b2-207">Consulte [esse problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obter informações sobre como acessar o Gerenciador de segredo do IIS.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cb8b2-208">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="cb8b2-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="cb8b2-209">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cb8b2-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cb8b2-210">Este documento explica técnicas para armazenar e recuperar dados confidenciais durante o desenvolvimento de um aplicativo de ASP.NET Core em um computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="cb8b2-211">Nunca armazene senhas ou outros dados confidenciais no código-fonte.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="cb8b2-212">Os segredos de produção não devem ser usados para desenvolvimento ou teste.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="cb8b2-213">Os segredos não devem ser implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="cb8b2-214">Em vez disso, os segredos devem ser disponibilizados no ambiente de produção por meio de um meio controlado, como variáveis de ambiente, Azure Key Vault, etc. Você pode armazenar e proteger os segredos de produção e de teste do Azure com o [provedor de configuração Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="cb8b2-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="cb8b2-215">Variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="cb8b2-215">Environment variables</span></span>

<span data-ttu-id="cb8b2-216">As variáveis de ambiente são usadas para evitar o armazenamento de segredos do aplicativo no código ou nos arquivos de configuração locais.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="cb8b2-217">Variáveis de ambiente substituem valores de configuração para todas as fontes de configuração especificadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="cb8b2-218">Considere um aplicativo Web ASP.NET Core no qual a segurança **das contas de usuário individuais** está habilitada.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="cb8b2-219">Uma cadeia de conexão de banco de dados padrão é incluída naappsettings.jsdo projeto \* no\* arquivo com a chave `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="cb8b2-220">A cadeia de conexão padrão é para o LocalDB, que é executado no modo de usuário e não requer uma senha.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="cb8b2-221">Durante a implantação do aplicativo, o `DefaultConnection` valor da chave pode ser substituído por um valor de variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="cb8b2-222">A variável de ambiente pode armazenar a cadeia de conexão completa com credenciais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="cb8b2-223">Variáveis de ambiente geralmente são armazenadas em texto simples e não criptografado.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="cb8b2-224">Se o computador ou o processo estiver comprometido, as variáveis de ambiente poderão ser acessadas por partes não confiáveis.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="cb8b2-225">Outras medidas para evitar a divulgação de segredos do usuário podem ser necessárias.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="cb8b2-226">Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="cb8b2-226">Secret Manager</span></span>

<span data-ttu-id="cb8b2-227">A ferramenta Gerenciador de segredo armazena dados confidenciais durante o desenvolvimento de um projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="cb8b2-228">Nesse contexto, um elemento de dados confidenciais é um segredo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="cb8b2-229">Os segredos do aplicativo são armazenados em um local separado da árvore do projeto.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="cb8b2-230">Os segredos do aplicativo são associados a um projeto específico ou compartilhados entre vários projetos.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="cb8b2-231">Os segredos do aplicativo não são verificados no controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="cb8b2-232">A ferramenta Gerenciador de segredo não criptografa os segredos armazenados e não deve ser tratada como um repositório confiável.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="cb8b2-233">É apenas para fins de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-233">It's for development purposes only.</span></span> <span data-ttu-id="cb8b2-234">As chaves e os valores são armazenados em um arquivo de configuração JSON no diretório de perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="cb8b2-235">Como funciona a ferramenta Gerenciador de segredo</span><span class="sxs-lookup"><span data-stu-id="cb8b2-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="cb8b2-236">A ferramenta Gerenciador de segredo abstrai os detalhes da implementação, como onde e como os valores são armazenados.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="cb8b2-237">Você pode usar a ferramenta sem conhecer esses detalhes de implementação.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="cb8b2-238">Os valores são armazenados em um arquivo de configuração JSON em uma pasta de perfil de usuário protegida pelo sistema no computador local:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="cb8b2-239">Windows</span><span class="sxs-lookup"><span data-stu-id="cb8b2-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="cb8b2-240">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="cb8b2-241">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="cb8b2-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="cb8b2-242">Caminho do sistema de arquivos:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="cb8b2-243">Nos caminhos de arquivo anteriores, substitua `<user_secrets_id>` pelo `UserSecretsId` valor especificado no arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="cb8b2-244">Não escreva o código que depende do local ou do formato dos dados salvos com a ferramenta Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="cb8b2-245">Esses detalhes de implementação podem ser alterados.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-245">These implementation details may change.</span></span> <span data-ttu-id="cb8b2-246">Por exemplo, os valores secretos não são criptografados, mas podem estar no futuro.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="cb8b2-247">Habilitar o armazenamento secreto</span><span class="sxs-lookup"><span data-stu-id="cb8b2-247">Enable secret storage</span></span>

<span data-ttu-id="cb8b2-248">A ferramenta Gerenciador de segredo opera em definições de configuração específicas do projeto armazenadas no seu perfil de usuário.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="cb8b2-249">Para usar os segredos do usuário, defina um `UserSecretsId` elemento dentro `PropertyGroup` de um do arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="cb8b2-250">O texto interno de `UserSecretsId` é arbitrário, mas é exclusivo para o projeto.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="cb8b2-251">Os desenvolvedores normalmente geram um GUID para o `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="cb8b2-252">No Visual Studio, clique com o botão direito do mouse no projeto em Gerenciador de Soluções e selecione **gerenciar segredos do usuário** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="cb8b2-253">Esse gesto adiciona um `UserSecretsId` elemento, populado com um GUID, ao arquivo *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="cb8b2-254">Definir um segredo</span><span class="sxs-lookup"><span data-stu-id="cb8b2-254">Set a secret</span></span>

<span data-ttu-id="cb8b2-255">Defina um segredo de aplicativo que consiste em uma chave e seu valor.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="cb8b2-256">O segredo é associado ao valor do projeto `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="cb8b2-257">Por exemplo, execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="cb8b2-258">No exemplo anterior, os dois pontos indica que `Movies` é um literal de objeto com uma `ServiceApiKey` propriedade.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="cb8b2-259">A ferramenta Gerenciador de segredo também pode ser usada em outros diretórios.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="cb8b2-260">Use a `--project` opção para fornecer o caminho do sistema de arquivos no qual o arquivo *. csproj* existe.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="cb8b2-261">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="cb8b2-262">Nivelamento de estrutura JSON no Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb8b2-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="cb8b2-263">O gesto **gerenciar segredos do usuário** do Visual Studio abre uma *secrets.jsno* arquivo no editor de texto.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="cb8b2-264">Substitua o conteúdo de *secrets.js* com os pares chave-valor a serem armazenados.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="cb8b2-265">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="cb8b2-266">A estrutura JSON é achatada após modificações via `dotnet user-secrets remove` ou `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="cb8b2-267">Por exemplo, `dotnet user-secrets remove "Movies:ConnectionString"` a execução recolhe o `Movies` literal do objeto.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="cb8b2-268">O arquivo modificado é semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="cb8b2-269">Definir vários segredos</span><span class="sxs-lookup"><span data-stu-id="cb8b2-269">Set multiple secrets</span></span>

<span data-ttu-id="cb8b2-270">Um lote de segredos pode ser definido por meio de tubulação JSON para o `set` comando.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="cb8b2-271">No exemplo a seguir, o *input.jsno* conteúdo do arquivo é canalizado para o `set` comando.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="cb8b2-272">Windows</span><span class="sxs-lookup"><span data-stu-id="cb8b2-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="cb8b2-273">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="cb8b2-274">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="cb8b2-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="cb8b2-275">Abra um shell de comando e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="cb8b2-276">Acessar um segredo</span><span class="sxs-lookup"><span data-stu-id="cb8b2-276">Access a secret</span></span>

<span data-ttu-id="cb8b2-277">A [API de configuração do ASP.NET Core](xref:fundamentals/configuration/index) fornece acesso aos segredos do Gerenciador de segredo.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="cb8b2-278">Se o projeto tiver como alvo .NET Framework, instale o [Microsoft.Extensions.Configuração. Pacote NuGet usersecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .</span><span class="sxs-lookup"><span data-stu-id="cb8b2-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="cb8b2-279">No ASP.NET Core 2,0 ou posterior, a fonte de configuração de segredos do usuário é adicionada automaticamente no modo de desenvolvimento quando o projeto chama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> para inicializar uma nova instância do host com padrões pré-configurados.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="cb8b2-280">`CreateDefaultBuilder` chama <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> quando o <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> é <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="cb8b2-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="cb8b2-281">Quando `CreateDefaultBuilder` não for chamado, adicione a fonte de configuração de segredos do usuário explicitamente chamando <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> no `Startup` Construtor.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="cb8b2-282">Chame `AddUserSecrets` somente quando o aplicativo for executado no ambiente de desenvolvimento, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="cb8b2-283">Os segredos do usuário podem ser recuperados por meio da `Configuration` API:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="cb8b2-284">Mapear segredos para um POCO</span><span class="sxs-lookup"><span data-stu-id="cb8b2-284">Map secrets to a POCO</span></span>

<span data-ttu-id="cb8b2-285">O mapeamento de um literal de objeto inteiro para um POCO (uma classe .NET simples com propriedades) é útil para agregar propriedades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="cb8b2-286">Para mapear os segredos anteriores para um POCO, use o `Configuration` recurso de associação de grafo de [objeto](xref:fundamentals/configuration/index#bind-to-an-object-graph) da API.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="cb8b2-287">O código a seguir é associado a um `MovieSettings` poco personalizado e acessa o `ServiceApiKey` valor da propriedade:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="cb8b2-288">Os `Movies:ConnectionString` `Movies:ServiceApiKey` segredos e são mapeados para as respectivas propriedades no `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="cb8b2-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="cb8b2-289">Substituição de cadeia de caracteres com segredos</span><span class="sxs-lookup"><span data-stu-id="cb8b2-289">String replacement with secrets</span></span>

<span data-ttu-id="cb8b2-290">O armazenamento de senhas em texto sem formatação não é seguro.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="cb8b2-291">Por exemplo, uma cadeia de conexão de banco de dados armazenada no *appsettings.jsno* pode incluir uma senha para o usuário especificado:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="cb8b2-292">Uma abordagem mais segura é armazenar a senha como um segredo.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="cb8b2-293">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="cb8b2-294">Remova o `Password` par chave-valor da cadeia de conexão no *appsettings.jsem*.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="cb8b2-295">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="cb8b2-296">O valor do segredo pode ser definido na propriedade de um <xref:System.Data.SqlClient.SqlConnectionStringBuilder> objeto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> para concluir a cadeia de conexão:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="cb8b2-297">Listar os segredos</span><span class="sxs-lookup"><span data-stu-id="cb8b2-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="cb8b2-298">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="cb8b2-299">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="cb8b2-300">No exemplo anterior, dois-pontos nos nomes de chave denota a hierarquia de objeto dentro de *secrets.jsem*.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="cb8b2-301">Remover um único segredo</span><span class="sxs-lookup"><span data-stu-id="cb8b2-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="cb8b2-302">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="cb8b2-303">Osecrets.jsdo aplicativo \* no\* arquivo foi modificado para remover o par chave-valor associado à `MoviesConnectionString` chave:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="cb8b2-304">`dotnet user-secrets list`A execução exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="cb8b2-305">Remover todos os segredos</span><span class="sxs-lookup"><span data-stu-id="cb8b2-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="cb8b2-306">Execute o seguinte comando do diretório no qual o arquivo *. csproj* existe:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="cb8b2-307">Todos os segredos do usuário para o aplicativo foram excluídos do *secrets.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="cb8b2-308">`dotnet user-secrets list`A execução exibe a seguinte mensagem:</span><span class="sxs-lookup"><span data-stu-id="cb8b2-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="cb8b2-309">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cb8b2-309">Additional resources</span></span>

* <span data-ttu-id="cb8b2-310">Consulte [esse problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obter informações sobre como acessar o Gerenciador de segredo do IIS.</span><span class="sxs-lookup"><span data-stu-id="cb8b2-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end