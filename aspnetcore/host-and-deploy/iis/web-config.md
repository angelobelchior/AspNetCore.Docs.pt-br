---
title: Arquivo web.config
author: rick-anderson
description: Descubra o que está dentro do arquivo de web.config e como configurar diferentes opções de módulo ASP.NET Core.
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
uid: host-and-deploy/iis/web-config
ms.openlocfilehash: 4d7305f7184745b66c5de6c86b907d419183cb3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755234"
---
# <a name="webconfig-file"></a>Arquivo `web.config`

O `web.config` é um arquivo que é lido pelo IIS e o [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para configurar um aplicativo hospedado com o IIS.

## <a name="webconfig-file-location"></a>`web.config` local do arquivo

Para configurar o [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) corretamente, o `web.config` arquivo deve estar presente no caminho [raiz do conteúdo](xref:fundamentals/index#content-root) (normalmente o caminho base do aplicativo) do aplicativo implantado. Esse é o mesmo local que o caminho físico do site fornecido ao IIS. O `web.config` arquivo é necessário na raiz do aplicativo para habilitar a publicação de vários aplicativos usando implantação da Web.

Existem arquivos confidenciais no caminho físico do aplicativo, como `{ASSEMBLY}.runtimeconfig.json` `{ASSEMBLY}.xml` (comentários de documentação XML) e `{ASSEMBLY}.deps.json` , em que o espaço reservado `{ASSEMBLY}` é o nome do assembly. Quando o `web.config` arquivo estiver presente e o site for iniciado normalmente, o IIS não atenderá a esses arquivos confidenciais se eles forem solicitados. Se o `web.config` arquivo estiver ausente, incorretamente nomeado ou não for possível configurar o site para inicialização normal, o IIS poderá fornecer arquivos confidenciais publicamente.

**O `web.config` arquivo deve estar presente na implantação em todos os momentos, corretamente nomeado e ser capaz de configurar o site para inicialização normal. Nunca remova o `web.config` arquivo de uma implantação de produção.**

Se um `web.config` arquivo não estiver presente no projeto, o arquivo será criado com o correto `processPath` e `arguments` para configurar o módulo ASP.NET Core e movido para a [saída publicada](xref:host-and-deploy/directory-structure).

Se um `web.config` arquivo estiver presente no projeto, o arquivo será transformado com o correto `processPath` e `arguments` para configurar o módulo ASP.NET Core e movido para a saída publicada. A transformação não altera as definições de configuração do IIS no arquivo.

O `web.config` arquivo pode fornecer definições de configuração adicionais do IIS que controlam os módulos do IIS ativos. Para saber mais sobre os módulos do IIS que podem processar solicitações com aplicativos do ASP.NET Core, veja o tópico [Módulos do IIS](xref:host-and-deploy/iis/modules).

Criar, transformar e publicar o `web.config` arquivo é tratado por um destino do MSBuild ( `_TransformWebConfig` ) quando o projeto é publicado. Este destino está presente nos destinos do SDK da Web (`Microsoft.NET.Sdk.Web`). O SDK é definido na parte superior do arquivo de projeto:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Para impedir que o SDK da Web transforme o `web.config` arquivo, use a `<IsTransformWebConfigDisabled>` propriedade no arquivo de projeto:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Ao desabilitar o SDK da Web da transformação do arquivo, o e o `processPath` `arguments` devem ser definidos manualmente pelo desenvolvedor. Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-aspnet-core-module-with-webconfig"></a>Configuração do módulo ASP.NET Core com `web.config`

O módulo ASP.NET Core é configurado com a `aspNetCore` seção do `system.webServer` nó no arquivo do site `web.config` .

O arquivo a seguir `web.config` é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o módulo ASP.NET Core para manipular solicitações de site:

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

O seguinte `web.config` é publicado para uma [implantação independente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):

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

A <xref:System.Configuration.SectionInformation.InheritInChildApplications%2A> propriedade é definida como `false` para indicar que as configurações especificadas no [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elemento não são herdadas por aplicativos que residem em um subdiretório do aplicativo.

Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`. O caminho salva os logs de stdout na `LogFiles` pasta, que é um local criado automaticamente pelo serviço.

Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Atributos do `aspNetCore` elemento

| Atributo | Descrição | Padrão |
| --------- | ----------- | :-----: |
| `arguments` | <p>Atributo de cadeia de caracteres opcional.</p><p>Argumentos para o executável especificado em `processPath` .</p> | |
| `disableStartUpErrorPage` | <p>Atributo booliano opcional.</p><p>Se for true, a página de **falha do processo 502,5** será suprimida e a página de código de status 502 configurada no `web.config` terá precedência.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Atributo booliano opcional.</p><p>Se for true, o token será encaminhado para o processo filho escutando `%ASPNETCORE_PORT%` como um cabeçalho ' MS-ASPNETCORE-WINAUTHTOKEN ' por solicitação. É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</p> | `true` |
| `hostingModel` | <p>Atributo de cadeia de caracteres opcional.</p><p>Especifica o modelo de hospedagem como em processo ( `InProcess` / `inprocess` ) ou fora do processo ( `OutOfProcess` / `outofprocess` ).</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>Atributo inteiro opcional.</p><p>Especifica o número de instâncias do processo especificado na `processPath` configuração que pode ser girada por aplicativo.</p><p>&dagger;Para hospedagem em processo, o valor está limitado a `1`.</p><p>A configuração `processesPerApplication` é desencorajada. Esse atributo será removido em uma versão futura.</p> | Padrão: `1`<br>Mín.: `1`<br>Máx.: `100`&dagger; |
| `processPath` | <p>Atributo de cadeia de caracteres obrigatório.</p><p>Caminho para o executável que inicia um processo que escuta solicitações HTTP. Caminhos relativos são compatíveis. Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</p> | |
| `rapidFailsPerMinute` | <p>Atributo inteiro opcional.</p><p>Especifica o número de vezes que o processo especificado em `processPath` tem permissão para falhar por minuto. Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</p><p>Sem suporte com hospedagem padrão.</p> | Padrão: `10`<br>Mín.: `0`<br>Máx.: `100` |
| `requestTimeout` | <p>Atributo de intervalo de tempo opcional.</p><p>Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</p><p>Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</p><p>Não se aplica à hospedagem em processo. Para a hospedagem em processo, o módulo aguarda o aplicativo processar a solicitação.</p><p>Os valores válidos para segmentos de minutos e segundos da cadeia de caracteres estão no intervalo 0 a 59. O uso de `60` no valor de minutos ou segundos resulta em um *erro de servidor interno 500*.</p> | Padrão: `00:02:00`<br>Mín.: `00:00:00`<br>Máx.: `360:00:00` |
| `shutdownTimeLimit` | <p>Atributo inteiro opcional.</p><p>Duração em segundos que o módulo aguarda até que o executável seja desligado normalmente quando o `app_offline.htm` arquivo é detectado.</p> | Padrão: `10`<br>Mín.: `0`<br>Máx.: `600` |
| `startupTimeLimit` | <p>Atributo inteiro opcional.</p><p>Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta. Se esse tempo limite é excedido, o módulo encerra o processo.</p><p>Ao hospedar *em processo*: o processo **não** é reiniciado e **não usa a** `rapidFailsPerMinute` configuração.</p><p>Ao hospedar *fora do processo*: o módulo tenta reinicializar o processo quando recebe uma nova solicitação e continua tentando reiniciar o processo em solicitações de entrada subsequentes, a menos que o aplicativo não consiga iniciar o `rapidFailsPerMinute` número de vezes no último minuto de reversão.</p><p>Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</p> | Padrão: `120`<br>Mín.: `0`<br>Máx.: `3600` |
| `stdoutLogEnabled` | <p>Atributo booliano opcional.</p><p>Se for true, `stdout` e `stderr` para o processo especificado em `processPath` será redirecionado para o arquivo especificado em `stdoutLogFile` .</p> | `false` |
| `stdoutLogFile` | <p>Atributo de cadeia de caracteres opcional.</p><p>Especifica o caminho de arquivo relativo ou absoluto para o qual `stdout` e `stderr` do processo especificado em `processPath` são registrados. Os caminhos relativos são relativos à raiz do site. Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos. Todas as pastas fornecidas no caminho são criadas pelo módulo quando o arquivo de log é criado. O uso de delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e uma extensão de arquivo ( `.log` ) são adicionados ao último segmento do `stdoutLogFile` caminho. Se `.\logs\stdout` for fornecido como um valor, um log de stdout de exemplo será salvo como `stdout_20180205194132_1934.log` na `logs` pasta quando salvo em 2/5/2018 às 19:41:32 com uma ID de processo de 1934.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`. Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`. Variáveis de ambiente definidas nesta seção têm precedência sobre variáveis de ambiente do sistema.

O exemplo a seguir define duas variáveis de ambiente no `web.config` . `ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`. Um desenvolvedor pode definir temporariamente esse valor no `web.config` arquivo para forçar a [página de exceção do desenvolvedor](xref:fundamentals/error-handling) a ser carregada durante a depuração de uma exceção de aplicativo. `CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.

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
> Uma alternativa para definir o ambiente diretamente no `web.config` é incluir a `<EnvironmentName>` propriedade no perfil de [publicação ( `.pubxml` )](xref:host-and-deploy/visual-studio-publish-profiles) ou no arquivo de projeto. Essa abordagem define o ambiente em `web.config` que o projeto é publicado:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.

## <a name="configuration-of-iis-with-webconfig"></a>Configuração do IIS com `web.config`

A configuração do IIS é influenciada pela `<system.webServer>` seção do `web.config` para cenários do IIS que são funcionais para aplicativos ASP.NET Core com o módulo ASP.NET Core. Por exemplo, a configuração do IIS é funcional para a compactação dinâmica. Se o IIS estiver configurado no nível do servidor para usar a compactação dinâmica, o `<urlCompression>` elemento no `web.config` arquivo do aplicativo poderá desabilitá-lo para um aplicativo ASP.NET Core.

Para obter mais informações, consulte estes tópicos:

* [Referência de configuração para `<system.webServer>`](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Para definir variáveis de ambiente para aplicativos individuais em execução em pools de aplicativos isolados (com suporte para o IIS 10,0 ou posterior), consulte a seção de * `AppCmd.exe` comando* do tópico [variáveis `<environmentVariables>` de ambiente](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) na documentação de referência do IIS.

## <a name="configuration-sections-of-webconfig"></a>Seções de configuração do `web.config`

As seções de configuração dos aplicativos ASP.NET 4. x `web.config` não são usadas pelos aplicativos ASP.NET Core para configuração:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Aplicativos ASP.NET Core são configurados para usar outros provedores de configuração. Para obter mais informações, consulte [Configuration](xref:fundamentals/configuration/index).

## <a name="transform-webconfig"></a>Transformação do Web.config

Se você precisar transformar `web.config` em publicar, consulte <xref:host-and-deploy/iis/transform-webconfig> . Talvez seja necessário transformar `web.config` em publicar para definir variáveis de ambiente com base na configuração, no perfil ou no ambiente.

## <a name="additional-resources"></a>Recursos adicionais

* [IIS \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/iis/modules>
* <xref:host-and-deploy/iis/transform-webconfig>
