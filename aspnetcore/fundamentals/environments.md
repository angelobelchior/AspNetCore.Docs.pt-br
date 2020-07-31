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
# <a name="use-multiple-environments-in-aspnet-core"></a>Usar vários ambientes no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)

O ASP.NET Core configura o comportamento do aplicativo com base no ambiente de runtime usando uma variável de ambiente.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Ambientes

Para determinar o ambiente de tempo de execução, o ASP.NET Core lê as seguintes variáveis de ambiente:

1. [DOTNET_ENVIRONMENT](xref:fundamentals/configuration/index#default-host-configuration)
1. `ASPNETCORE_ENVIRONMENT`Quando <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> é chamado. A chamada de modelos de aplicativo Web ASP.NET Core padrão `ConfigureWebHostDefaults` . O `ASPNETCORE_ENVIRONMENT` valor é substituído `DOTNET_ENVIRONMENT` .

`IHostEnvironment.EnvironmentName`pode ser definido como qualquer valor, mas os seguintes valores são fornecidos pela estrutura:

* <xref:Microsoft.Extensions.Hosting.Environments.Development>: O [launchSettings.jsem](#lsj) conjuntos de arquivos `ASPNETCORE_ENVIRONMENT` para `Development` no computador local.
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production>: O padrão if `DOTNET_ENVIRONMENT` e `ASPNETCORE_ENVIRONMENT` não foram definidos.

O seguinte código:

* Chama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` é definido como `Development`.
* Chama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando o valor de `ASPNETCORE_ENVIRONMENT` é definido como `Staging` , `Production` ou `Staging_2` .
* Injeta <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> em `Startup.Configure` . Essa abordagem é útil quando o aplicativo requer apenas o ajuste `Startup.Configure` para alguns ambientes com diferenças mínimas de código por ambiente.
* É semelhante ao código gerado pelos modelos de ASP.NET Core.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

O [auxiliar de marca de ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa o valor de [IHostEnvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) para incluir ou excluir marcação no elemento:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

A [página About](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) do [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) inclui a marcação anterior e exibe o valor de `IWebHostEnvironment.EnvironmentName` .

No Windows e no macOS, variáveis de ambiente e valores não diferenciam maiúsculas de minúsculas. Variáveis e valores de ambiente do Linux diferenciam **maiúsculas de minúsculas** por padrão.

### <a name="create-environmentssample"></a>Criar EnvironmentsSample

O [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) usado neste documento é baseado em um Razor projeto de páginas chamado *EnvironmentsSample*.

O código a seguir cria e executa um aplicativo Web chamado *EnvironmentsSample*:

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

Quando o aplicativo é executado, ele exibe alguns dos seguintes resultados:

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

### <a name="development-and-launchsettingsjson"></a>Desenvolvimento e launchSettings.jsem

O ambiente de desenvolvimento pode habilitar recursos que não devem ser expostos em produção. Por exemplo, os modelos do ASP.NET Core habilitam a [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page) no ambiente de desenvolvimento.

O ambiente de desenvolvimento do computador local pode ser definido no arquivo *Properties\launchSettings.json* do projeto. Os valores de ambiente definidos em *launchSettings.json* substituem os valores definidos no ambiente do sistema.

O *launchSettings.jsno* arquivo:

* É usado somente no computador de desenvolvimento local.
* Não está implantado.
* contém configurações de perfil.

O JSON a seguir mostra o *launchSettings.jsno* arquivo para um projeto Web ASP.NET Core chamado *EnvironmentsSample* criado com o Visual Studio ou `dotnet new` :

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

A marcação anterior contém dois perfis:

* `IIS Express`: O perfil padrão usado ao iniciar o aplicativo no Visual Studio. A `"commandName"` chave tem o valor `"IISExpress"` , portanto, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) é o servidor Web. Você pode definir o perfil de inicialização para o projeto ou qualquer outro perfil incluído. Por exemplo, na imagem abaixo, selecionar o nome do projeto inicia o [servidor Web Kestrel](xref:fundamentals/servers/kestrel).

  ![IIS Express iniciar no menu](environments/_static/iisx2.png)
* `EnvironmentsSample`: O nome do perfil é o nome do projeto. Esse perfil é usado por padrão ao iniciar o aplicativo com o `dotnet run` .  A `"commandName"` chave tem o valor `"Project"` , portanto, o [servidor Web Kestrel](xref:fundamentals/servers/kestrel) é iniciado.

O valor de `commandName` pode especificar o servidor Web a ser iniciado. `commandName` pode ser qualquer um dos seguintes:

* `IISExpress`: Inicia IIS Express.
* `IIS`: Nenhum servidor Web foi iniciado. Espera-se que o IIS esteja disponível.
* `Project`: Inicia o Kestrel.

A guia **depurar** das propriedades do projeto do Visual Studio fornece uma GUI para editar o *launchSettings.jsno* arquivo. As alterações feitas nos perfis do projeto poderão não ter efeito até que o servidor Web seja reiniciado. O Kestrel precisa ser reiniciado antes de detectar as alterações feitas ao seu ambiente.

![Configurando variáveis de ambiente das propriedades do projeto](environments/_static/project-properties-debug.png)

O seguinte *launchSettings.jsno* arquivo contém vários perfis:

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

Os perfis podem ser selecionados:

* Na interface do usuário do Visual Studio.
* Usando o [`dotnet run`](/dotnet/core/tools/dotnet-run) comando em um shell de comando com a `--launch-profile` opção definida como o nome do perfil. *Essa abordagem dá suporte apenas a perfis Kestrel.*

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> *launchSettings.json* não deve armazenar segredos. A [ferramenta Secret Manager](xref:security/app-secrets) pode ser usado para armazenar segredos de desenvolvimento local.

Ao usar [Visual Studio Code](https://code.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json*. O exemplo a seguir define várias [variáveis de ambiente de valores de configuração de host](xref:fundamentals/host/web-host#host-configuration-values):

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

O *. vscode/launch.jsno* arquivo é usado somente pelo Visual Studio Code.

### <a name="production"></a>Produção

O ambiente de produção deve ser configurado para maximizar a segurança, o [desempenho](xref:performance/performance-best-practices)e a robustez do aplicativo. Algumas configurações comuns que são diferentes do desenvolvimento incluem:

* [Caching](xref:performance/caching/memory).
* Recursos do lado do cliente são agrupados, minimizados e potencialmente atendidos por meio de uma CDN.
* Páginas de erro de diagnóstico desabilitadas.
* Páginas de erro amigáveis habilitadas.
* Monitoramento e [log](xref:fundamentals/logging/index) de produção habilitados. Por exemplo, usando [Application insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Definir o ambiente

Geralmente, é útil definir um ambiente específico para teste com uma variável de ambiente ou configuração de plataforma. Se o ambiente não for definido, ele usará `Production` como padrão, o que desabilitará a maioria dos recursos de depuração. O método para configurar o ambiente depende do sistema operacional.

Quando o host é criado, a última configuração de ambiente lida pelo aplicativo determina o ambiente do aplicativo. O ambiente do aplicativo não pode ser alterado enquanto o aplicativo está em execução.

A [página About](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) do [código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) exibe o valor de `IWebHostEnvironment.EnvironmentName` .

### <a name="azure-app-service"></a>Serviço de aplicativo do Azure

Para definir o ambiente no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), execute as seguintes etapas:

1. Selecione o aplicativo na folha **Serviços de Aplicativos**.
1. No grupo **configurações** , selecione a folha **configuração** .
1. Na guia **configurações do aplicativo** , selecione **nova configuração de aplicativo**.
1. Na janela **Adicionar/Editar configuração de aplicativo** , forneça `ASPNETCORE_ENVIRONMENT` o **nome**. Para **valor**, forneça o ambiente (por exemplo, `Staging` ).
1. Marque a caixa de seleção **configuração do slot de implantação** se desejar que a configuração do ambiente permaneça com o slot atual quando os slots de implantação forem trocados. Para obter mais informações, consulte [configurar ambientes de preparo no serviço de Azure app](/azure/app-service/web-sites-staged-publishing) na documentação do Azure.
1. Selecione **OK** para fechar a janela **Adicionar/Editar configuração de aplicativo** .
1. Selecione **salvar** na parte superior da folha de **configuração** .

Azure App serviço reinicia automaticamente o aplicativo depois que uma configuração de aplicativo é adicionada, alterada ou excluída no portal do Azure.

### <a name="windows"></a>Windows

Valores de ambiente em *launchSettings.jsem valores de* substituição definidos no ambiente do sistema.

Para definir o `ASPNETCORE_ENVIRONMENT` para a sessão atual quando o aplicativo for iniciado usando [dotnet run](/dotnet/core/tools/dotnet-run), os comandos a seguir serão usados:

**Prompt de comando**

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

O comando anterior define `ASPNETCORE_ENVIRONMENT` somente para processos iniciados a partir dessa janela de comando.

Para definir o valor globalmente no Windows, use uma das seguintes abordagens:

* Abra o **Painel de Controle** > **Sistema** > **Configurações avançadas do sistema** e adicione ou edite o valor `ASPNETCORE_ENVIRONMENT`:

  ![Propriedades avançadas do sistema](environments/_static/systemsetting_environment.png)

  ![Variável de ambiente do ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* Abra um prompt de comando administrativo e use o comando `setx` ou abra um prompt de comando administrativo do PowerShell e use `[Environment]::SetEnvironmentVariable`:

  **Prompt de comando**

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  O comutador `/M` indica para definir a variável de ambiente no nível do sistema. Se o comutador `/M` não for usado, a variável de ambiente será definida para a conta de usuário.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  O valor da opção `Machine` indica para definir a variável de ambiente no nível do sistema. Se o valor da opção for alterado para `User`, a variável de ambiente será definida para a conta de usuário.

Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida globalmente, ela entra em vigor para `dotnet run` em qualquer janela de comando aberta depois que o valor é definido. Valores de ambiente em *launchSettings.jsem valores de* substituição definidos no ambiente do sistema.

**web.config**

Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` com *web.config*, consulte a seção *Definindo variáveis de ambiente* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Arquivo de projeto ou perfil de publicação**

**Para implantações do Windows IIS:** Inclua a `<EnvironmentName>` propriedade no [perfil de publicação (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou no arquivo de projeto. Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Por pool de aplicativos do IIS**

Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` para um aplicativo em execução em um Pool de aplicativos isolado (compatível com IIS 10.0 ou posterior), consulte a seção *Comando AppCmd.exe* do tópico [Variáveis de ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe). Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida para um pool de aplicativos, seu valor substitui uma configuração no nível do sistema.

Ao hospedar um aplicativo no IIS e adicionar ou alterar a variável de ambiente `ASPNETCORE_ENVIRONMENT`, use qualquer uma das abordagens a seguir para que o novo valor seja escolhido por aplicativos:

* Execute `net stop was /y` seguido por `net start w3svc` em um prompt de comando.
* Reinicie o servidor.

#### <a name="macos"></a>macOS

A configuração do ambiente atual para macOS pode ser feita em linha ao executar o aplicativo:

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

Como alternativa, defina o ambiente com `export` antes de executar o aplicativo:

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

As variáveis de ambiente no nível do computador são definidas no arquivo *.bashrc* ou *.bash_profile*. Edite o arquivo usando qualquer editor de texto. Adicione a seguinte instrução:

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a>Linux

Para distribuições do Linux, use o `export` comando em um prompt de comando para configurações de variáveis baseadas em sessão e *bash_profile* arquivo para configurações de ambiente de nível de máquina.

### <a name="set-the-environment-in-code"></a>Definir o ambiente no código

Chame <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> ao compilar o host. Consulte <xref:fundamentals/host/generic-host#environmentname>.

### <a name="configuration-by-environment"></a>Configuração por ambiente

Para carregar a configuração por ambiente, consulte <xref:fundamentals/configuration/index#json-configuration-provider> .

## <a name="environment-based-startup-class-and-methods"></a>Métodos e classe Startup baseados no ambiente

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>Injetar IWebHostEnvironment na classe de inicialização

Injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> no `Startup` Construtor. Essa abordagem é útil quando o aplicativo requer configuração `Startup` para apenas alguns ambientes com diferenças mínimas de código por ambiente.

No exemplo a seguir:

* O ambiente é mantido no `_env` campo.
* `_env`é usado no `ConfigureServices` e `Configure` para aplicar a configuração de inicialização com base no ambiente do aplicativo.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a>Convenções da classe Startup

Quando um aplicativo ASP.NET Core é iniciado, a [classe Startup](xref:fundamentals/startup) inicia o aplicativo. O aplicativo pode definir várias `Startup` classes para ambientes diferentes. A `Startup` classe apropriada está selecionada em tempo de execução. A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada. Se uma classe `Startup{EnvironmentName}` correspondente não for encontrada, a classe `Startup` será usada. Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente. Aplicativos típicos não precisarão dessa abordagem.

Para implementar classes baseadas em ambiente `Startup` , crie `Startup{EnvironmentName}` classes e uma classe de fallback `Startup` :

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

Use a sobrecarga [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que aceita um nome de assembly:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a>Convenções do método Startup

[Configurar](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [configuraservices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) oferecem suporte a versões específicas do ambiente do formulário `Configure<EnvironmentName>` e `Configure<EnvironmentName>Services` . Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT)

O ASP.NET Core configura o comportamento do aplicativo com base no ambiente de runtime usando uma variável de ambiente.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Ambientes

O ASP.NET Core lê a variável de ambiente `ASPNETCORE_ENVIRONMENT` na inicialização do aplicativo e armazena o valor em [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`pode ser definido como qualquer valor, mas três valores são fornecidos pela estrutura:

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (padrão)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

O código anterior:

* Chama [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quando `ASPNETCORE_ENVIRONMENT` é definido como `Development`.
* Chama [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quando o valor de `ASPNETCORE_ENVIRONMENT` é definido com um dos seguintes:

  * `Staging`
  * `Production`
  * `Staging_2`

O [auxiliar de marca de ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) usa o valor de `IHostingEnvironment.EnvironmentName` para incluir ou excluir marcação no elemento:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

No Windows e no macOS, variáveis de ambiente e valores não diferenciam maiúsculas de minúsculas. Variáveis e valores de ambiente do Linux diferenciam maiúsculas de minúsculas por padrão.

### <a name="development"></a>Desenvolvimento

O ambiente de desenvolvimento pode habilitar recursos que não devem ser expostos em produção. Por exemplo, os modelos do ASP.NET Core habilitam a [Página de exceção do desenvolvedor](xref:fundamentals/error-handling#developer-exception-page) no ambiente de desenvolvimento.

O ambiente de desenvolvimento do computador local pode ser definido no arquivo *Properties\launchSettings.json* do projeto. Os valores de ambiente definidos em *launchSettings.json* substituem os valores definidos no ambiente do sistema.

O seguinte JSON mostra três perfis de um arquivo *launchSettings.json*:

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
> A propriedade `applicationUrl` no *launchSettings.json* pode especificar uma lista de URLs de servidores. Use um ponto e vírgula entre as URLs na lista:
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

Quando o aplicativo é inicializado com [dotnet run](/dotnet/core/tools/dotnet-run), o primeiro perfil com `"commandName": "Project"` é usado. O valor de `commandName` especifica o servidor Web a ser iniciado. `commandName` pode ser qualquer um dos seguintes:

* `IISExpress`
* `IIS`
* `Project` (que inicia o Kestrel)

Quando um aplicativo for iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):

* *launchSettings.json* é lido, se está disponível. As configurações de `environmentVariables` em *launchSettings.json* substituem as variáveis de ambiente.
* O ambiente de hospedagem é exibido.

A saída a seguir mostra um aplicativo iniciado com [dotnet run](/dotnet/core/tools/dotnet-run):

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

A guia **Depurar** das propriedades do projeto do Visual Studio fornece uma GUI para editar o arquivo *launchSettings.json*:

![Configurando variáveis de ambiente das propriedades do projeto](environments/_static/project-properties-debug.png)

As alterações feitas nos perfis do projeto poderão não ter efeito até que o servidor Web seja reiniciado. O Kestrel precisa ser reiniciado antes de detectar as alterações feitas ao seu ambiente.

> [!WARNING]
> *launchSettings.json* não deve armazenar segredos. A [ferramenta Secret Manager](xref:security/app-secrets) pode ser usado para armazenar segredos de desenvolvimento local.

Ao usar [Visual Studio Code](https://code.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *.vscode/launch.json*. O exemplo a seguir define o ambiente como `Development`:

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

Um arquivo *.vscode/launch.json* do projeto não é lido ao iniciar o aplicativo com `dotnet run` da mesma maneira que *Properties/launchSettings.json*. Ao inicializar um aplicativo em desenvolvimento que não tem um arquivo *launchSettings.json*, defina o ambiente com uma variável de ambiente ou um argumento de linha de comando para o comando `dotnet run`.

### <a name="production"></a>Produção

O ambiente de produção deve ser configurado para maximizar a segurança, o desempenho e a robustez do aplicativo. Algumas configurações comuns que são diferentes do desenvolvimento incluem:

* Armazenamento em cache.
* Recursos do lado do cliente são agrupados, minimizados e potencialmente atendidos por meio de uma CDN.
* Páginas de erro de diagnóstico desabilitadas.
* Páginas de erro amigáveis habilitadas.
* Log de produção e monitoramento habilitados. Por exemplo, [Application insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Definir o ambiente

Geralmente, é útil definir um ambiente específico para teste com uma variável de ambiente ou configuração de plataforma. Se o ambiente não for definido, ele usará `Production` como padrão, o que desabilitará a maioria dos recursos de depuração. O método para configurar o ambiente depende do sistema operacional.

Quando o host é criado, a última configuração de ambiente lida pelo aplicativo determina o ambiente do aplicativo. O ambiente do aplicativo não pode ser alterado enquanto o aplicativo está em execução.

### <a name="environment-variable-or-platform-setting"></a>Configuração de plataforma ou variável de ambiente

#### <a name="azure-app-service"></a>Serviço de aplicativo do Azure

Para definir o ambiente no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), execute as seguintes etapas:

1. Selecione o aplicativo na folha **Serviços de Aplicativos**.
1. No grupo **configurações** , selecione a folha **configuração** .
1. Na guia **configurações do aplicativo** , selecione **nova configuração de aplicativo**.
1. Na janela **Adicionar/Editar configuração de aplicativo** , forneça `ASPNETCORE_ENVIRONMENT` o **nome**. Para **valor**, forneça o ambiente (por exemplo, `Staging` ).
1. Marque a caixa de seleção **configuração do slot de implantação** se desejar que a configuração do ambiente permaneça com o slot atual quando os slots de implantação forem trocados. Para obter mais informações, consulte [configurar ambientes de preparo no serviço de Azure app](/azure/app-service/web-sites-staged-publishing) na documentação do Azure.
1. Selecione **OK** para fechar a janela **Adicionar/Editar configuração de aplicativo** .
1. Selecione **salvar** na parte superior da folha de **configuração** .

O Serviço de Aplicativo do Azure reinicia automaticamente o aplicativo após uma configuração de aplicativo (variável de ambiente) ser adicionada, alterada ou excluída no portal do Azure.

#### <a name="windows"></a>Windows

Para definir o `ASPNETCORE_ENVIRONMENT` para a sessão atual quando o aplicativo for iniciado usando [dotnet run](/dotnet/core/tools/dotnet-run), os comandos a seguir serão usados:

**Prompt de comando**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Esses comandos somente têm efeito para a janela atual. Quando a janela é fechada, a configuração `ASPNETCORE_ENVIRONMENT` é revertida para a configuração padrão ou o valor de computador.

Para definir o valor globalmente no Windows, use uma das seguintes abordagens:

* Abra o **Painel de Controle** > **Sistema** > **Configurações avançadas do sistema** e adicione ou edite o valor `ASPNETCORE_ENVIRONMENT`:

  ![Propriedades avançadas do sistema](environments/_static/systemsetting_environment.png)

  ![Variável de ambiente do ASP.NET Core](environments/_static/windows_aspnetcore_environment.png)

* Abra um prompt de comando administrativo e use o comando `setx` ou abra um prompt de comando administrativo do PowerShell e use `[Environment]::SetEnvironmentVariable`:

  **Prompt de comando**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  O comutador `/M` indica para definir a variável de ambiente no nível do sistema. Se o comutador `/M` não for usado, a variável de ambiente será definida para a conta de usuário.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  O valor da opção `Machine` indica para definir a variável de ambiente no nível do sistema. Se o valor da opção for alterado para `User`, a variável de ambiente será definida para a conta de usuário.

Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida globalmente, ela entra em vigor para `dotnet run` em qualquer janela de comando aberta depois que o valor é definido.

**web.config**

Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` com *web.config*, consulte a seção *Definindo variáveis de ambiente* de <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Arquivo de projeto ou perfil de publicação**

**Para implantações do Windows IIS:** Inclua a `<EnvironmentName>` propriedade no [perfil de publicação (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou no arquivo de projeto. Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Por pool de aplicativos do IIS**

Para definir a variável de ambiente `ASPNETCORE_ENVIRONMENT` para um aplicativo em execução em um Pool de aplicativos isolado (compatível com IIS 10.0 ou posterior), consulte a seção *Comando AppCmd.exe* do tópico [Variáveis de ambiente &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe). Quando a variável de ambiente `ASPNETCORE_ENVIRONMENT` é definida para um pool de aplicativos, seu valor substitui uma configuração no nível do sistema.

> [!IMPORTANT]
> Ao hospedar um aplicativo no IIS e adicionar ou alterar a variável de ambiente `ASPNETCORE_ENVIRONMENT`, use qualquer uma das abordagens a seguir para que o novo valor seja escolhido por aplicativos:
>
> * Execute `net stop was /y` seguido por `net start w3svc` em um prompt de comando.
> * Reinicie o servidor.

#### <a name="macos"></a>macOS

A configuração do ambiente atual para macOS pode ser feita em linha ao executar o aplicativo:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Como alternativa, defina o ambiente com `export` antes de executar o aplicativo:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

As variáveis de ambiente no nível do computador são definidas no arquivo *.bashrc* ou *.bash_profile*. Edite o arquivo usando qualquer editor de texto. Adicione a seguinte instrução:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Para distribuições do Linux, use o `export` comando em um prompt de comando para configurações de variáveis baseadas em sessão e *bash_profile* arquivo para configurações de ambiente de nível de máquina.

### <a name="set-the-environment-in-code"></a>Definir o ambiente no código

Chame <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> ao compilar o host. Consulte <xref:fundamentals/host/web-host#environment>.

### <a name="configuration-by-environment"></a>Configuração por ambiente

Para carregar a configuração por ambiente, recomendamos:

* *appSettings* arquivos (*appSettings. { Ambiente}. JSON*). Consulte <xref:fundamentals/configuration/index#json-configuration-provider>.
* Variáveis de ambiente (definidas em cada sistema em que o aplicativo está hospedado). Veja <xref:fundamentals/host/web-host#environment> e <xref:security/app-secrets#environment-variables>.
* Gerenciador de Segredo (somente no ambiente de desenvolvimento). Consulte <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Métodos e classe Startup baseados no ambiente

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>Injetar IHostingEnvironment em Startup.Configlhar

Injetar <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> no `Startup.Configure` . Essa abordagem é útil quando o aplicativo requer apenas a configuração `Startup.Configure` para alguns ambientes com diferenças mínimas de código por ambiente.

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>Injetar IHostingEnvironment na classe de inicialização

Injetar <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> no `Startup` Construtor e atribuir o serviço a um campo para uso em toda a `Startup` classe. Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para apenas alguns ambientes com diferenças mínimas de código por ambiente.

No exemplo a seguir:

* O ambiente é mantido no `_env` campo.
* `_env`é usado no `ConfigureServices` e `Configure` para aplicar a configuração de inicialização com base no ambiente do aplicativo.

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

### <a name="startup-class-conventions"></a>Convenções da classe Startup

Quando um aplicativo ASP.NET Core é iniciado, a [classe Startup](xref:fundamentals/startup) inicia o aplicativo. O aplicativo pode definir `Startup` classes separadas para ambientes diferentes (por exemplo, `StartupDevelopment` ). A `Startup` classe apropriada está selecionada em tempo de execução. A classe cujo sufixo do nome corresponde ao ambiente atual é priorizada. Se uma classe `Startup{EnvironmentName}` correspondente não for encontrada, a classe `Startup` será usada. Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.

Para implementar classes `Startup` com base em ambiente, crie uma classe `Startup{EnvironmentName}` para cada ambiente no uso e classe `Startup` de fallback:

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

Use a sobrecarga [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) que aceita um nome de assembly:

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

### <a name="startup-method-conventions"></a>Convenções do método Startup

[Configurar](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) e [configuraservices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) oferecem suporte a versões específicas do ambiente do formulário `Configure<EnvironmentName>` e `Configure<EnvironmentName>Services` . Essa abordagem é útil quando o aplicativo requer a configuração da inicialização para vários ambientes com muitas diferenças de código por ambiente.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
