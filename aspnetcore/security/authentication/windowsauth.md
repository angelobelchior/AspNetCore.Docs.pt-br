---
title: Configurar a autenticação do Windows no ASP.NET Core
author: scottaddie
description: Saiba como configurar a autenticação do Windows no ASP.NET Core para IIS e HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330672"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Configurar a autenticação do Windows no ASP.NET Core

Por [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

A autenticação do Windows (também conhecida como autenticação Negotiate, Kerberos ou NTLM) pode ser configurada para aplicativos ASP.NET Core hospedados com [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel)ou [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

A autenticação do Windows (também conhecida como autenticação de negociação, Kerberos ou NTLM) pode ser configurada para aplicativos ASP.NET Core hospedados com o [IIS](xref:host-and-deploy/iis/index) ou [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

A autenticação do Windows depende do sistema operacional para autenticar usuários de ASP.NET Core aplicativos. Você pode usar a autenticação do Windows quando o servidor é executado em uma rede corporativa usando Active Directory identidades de domínio ou contas do Windows para identificar os usuários. A autenticação do Windows é mais adequada para ambientes de intranet em que os usuários, aplicativos cliente e servidores Web pertencem ao mesmo domínio do Windows.

> [!NOTE]
> Não há suporte para a autenticação do Windows com HTTP/2. Os desafios de autenticação podem ser enviados em respostas HTTP/2, mas o cliente deve fazer o downgrade para HTTP/1.1 antes de autenticar.

## <a name="proxy-and-load-balancer-scenarios"></a>Cenários de balanceador de carga e proxy

A autenticação do Windows é um cenário com estado usado principalmente em uma intranet, em que um proxy ou balanceador de carga geralmente não trata o tráfego entre clientes e servidores. Se um proxy ou um balanceador de carga for usado, a autenticação do Windows só funcionará se o proxy ou o balanceador de carga:

* Manipula a autenticação.
* Passa as informações de autenticação do usuário para o aplicativo (por exemplo, em um cabeçalho de solicitação), que atua nas informações de autenticação.

Uma alternativa à autenticação do Windows em ambientes em que proxies e balanceadores de carga são usados é Active Directory serviços federados (ADFS) com o OpenID Connect (OIDC).

## <a name="iisiis-express"></a>IIS/IIS Express

Adicione serviços de autenticação invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) em `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>Configurações de inicialização (depurador)

A configuração das configurações de inicialização afeta apenas as *Propriedades/launchSettings.jsno* arquivo para IIS Express e não configura o IIS para autenticação do Windows. A configuração do servidor é explicada na seção [IIS](#iis) .

O modelo de **aplicativo Web** disponível por meio do Visual Studio ou do CLI do .NET Core pode ser configurado para dar suporte à autenticação do Windows, que atualiza as *Propriedades/launchSettings.jsautomaticamente no* arquivo.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Novo Projeto**

1. Criar um novo projeto.
1. Selecione **Aplicativo Web ASP.NET Core**. Selecione **Avançar**.
1. Forneça um nome no campo **nome do projeto** . Confirme se a entrada de **local** está correta ou forneça um local para o projeto. Selecione **Criar**.
1. Selecione **alterar** em **autenticação**.
1. Na janela **alterar autenticação** , selecione **autenticação do Windows**. Selecione **OK**.
1. Selecione **aplicativo Web**.
1. Selecione **Criar**.

Execute o aplicativo. O nome de usuário aparece na interface do usuário do aplicativo renderizado.

**Projeto existente**

As propriedades do projeto habilitam a autenticação do Windows e desabilitam a autenticação anônima:

1. Clique com o botão direito do mouse no projeto em **Gerenciador de soluções** e selecione **Propriedades**.
1. Selecione a guia **Depurar**.
1. Desmarque a caixa de seleção **habilitar autenticação anônima**.
1. Marque a caixa de seleção para **habilitar a autenticação do Windows**.
1. Salve e feche a página de propriedades.

Como alternativa, as propriedades podem ser configuradas no `iisSettings` nó do *launchSettings.jsno* arquivo:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

**Novo Projeto**

Execute o comando [dotnet New](/dotnet/core/tools/dotnet-new) com o `webapp` argumento (ASP.NET Core aplicativo Web) e o `--auth Windows` comutador:

```dotnetcli
dotnet new webapp --auth Windows
```

**Projeto existente**

Atualize o `iisSettings` nó do *launchSettings.jsno* arquivo:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

Ao modificar um projeto existente, confirme se o arquivo de projeto inclui uma referência de pacote para o [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) **ou** o pacote NuGet [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) .

### <a name="iis"></a>IIS

O IIS usa o [módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para hospedar ASP.NET Core aplicativos. A autenticação do Windows é configurada para o IIS por meio do arquivo *web.config* . As seções a seguir mostram como:

* Forneça um arquivo de *web.config* local que ativa a autenticação do Windows no servidor quando o aplicativo é implantado.
* Use o Gerenciador do IIS para configurar o arquivo de *web.config* de um aplicativo ASP.NET Core que já foi implantado no servidor.

Se você ainda não tiver feito isso, habilite o IIS para hospedar ASP.NET Core aplicativos. Para obter mais informações, consulte <xref:host-and-deploy/iis/index>.

Habilite o serviço de função do IIS para autenticação do Windows. Para obter mais informações, consulte [habilitar a autenticação do Windows nos serviços de função do IIS (consulte a etapa 2)](xref:host-and-deploy/iis/index#iis-configuration).

O [middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) é configurado para autenticar automaticamente as solicitações por padrão. Para obter mais informações, consulte [Host ASP.NET Core no Windows com IIS: opções do IIS (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).

O módulo ASP.NET Core é configurado para encaminhar o token de autenticação do Windows para o aplicativo por padrão. Para obter mais informações, consulte [referência de configuração de módulo ASP.NET Core: atributos do elemento aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

Use **uma** das seguintes abordagens:

* **Antes de publicar e implantar o projeto,** adicione o seguinte arquivo de *web.config* à raiz do projeto:

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  Quando o projeto é publicado pelo SDK do .NET Core (sem a `<IsTransformWebConfigDisabled>` propriedade definida como `true` no arquivo de projeto), o arquivo de *web.config* publicado inclui a `<location><system.webServer><security><authentication>` seção. Para obter mais informações sobre a `<IsTransformWebConfigDisabled>` propriedade, consulte <xref:host-and-deploy/iis/index#webconfig-file> .

* **Depois de publicar e implantar o projeto,** execute a configuração do lado do servidor com o Gerenciador do IIS:

  1. No Gerenciador do IIS, selecione o site do IIS no nó **sites** da barra lateral **conexões** .
  1. Clique duas vezes em **autenticação** na área do **IIS** .
  1. Selecione **autenticação anônima**. Selecione **desabilitar** na barra lateral **ações** .
  1. Selecione **Autenticação do Windows**. Selecione **habilitar** na barra lateral **ações** .

  Quando essas ações são realizadas, o Gerenciador do IIS modifica o arquivo de *web.config* do aplicativo. Um `<system.webServer><security><authentication>` nó é adicionado com configurações atualizadas para `anonymousAuthentication` e `windowsAuthentication` :

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  A `<system.webServer>` seção adicionada ao arquivo de *web.config* pelo Gerenciador do IIS está fora da seção do aplicativo `<location>` adicionada pelo SDK do .NET Core quando o aplicativo é publicado. Como a seção é adicionada fora do `<location>` nó, as configurações são herdadas por qualquer [subaplicativo](xref:host-and-deploy/iis/index#sub-applications) para o aplicativo atual. Para impedir a herança, mova a `<security>` seção adicionada dentro da `<location><system.webServer>` seção que o SDK do .NET Core fornecido.

  Quando o Gerenciador do IIS é usado para adicionar a configuração do IIS, ele afeta apenas o arquivo de *web.config* do aplicativo no servidor. Uma implantação subsequente do aplicativo poderá substituir as configurações no servidor se a cópia do servidor de *web.config* for substituída pelo arquivo de *web.config* do projeto. Use **uma** das seguintes abordagens para gerenciar as configurações:

  * Use o Gerenciador do IIS para redefinir as configurações no arquivo de *web.config* depois que o arquivo for substituído na implantação.
  * Adicione um *arquivo deweb.config* ao aplicativo localmente com as configurações.

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a>Kestrel

O pacote NuGet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) pode ser usado com o [Kestrel](xref:fundamentals/servers/kestrel) para dar suporte à autenticação do Windows usando Negotiate e Kerberos no Windows, Linux e MacOS.

> [!WARNING]
> As credenciais podem ser mantidas entre solicitações em uma conexão. *A autenticação de negociação não deve ser usada com proxies, a menos que o proxy mantenha uma afinidade de conexão 1:1 (uma conexão persistente) com Kestrel.*

> [!NOTE]
> O manipulador Negotiate detecta se o servidor subjacente dá suporte à autenticação do Windows nativamente e se está habilitado. Se o servidor oferecer suporte à autenticação do Windows, mas estiver desabilitado, um erro será gerado solicitando que você habilite a implementação do servidor. Quando a autenticação do Windows está habilitada no servidor, o manipulador Negotiate encaminha-se de forma transparente para ele.

Adicione serviços de autenticação invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> e <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> em `Startup.ConfigureServices` :

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

Adicione o middleware de autenticação chamando <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> em `Startup.Configure` :

 ```csharp
app.UseAuthentication();
```

Para obter mais informações sobre o middleware, consulte <xref:fundamentals/middleware/index> .

Solicitações anônimas são permitidas. Use [ASP.NET Core autorização](xref:security/authorization/introduction) para desafiar solicitações anônimas de autenticação.

### <a name="windows-environment-configuration"></a>Configuração de ambiente do Windows

O componente [Microsoft. AspNetCore. Authentication. Negotiation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) executa a autenticação de modo de usuário. Os SPNs (nomes da entidade de serviço) devem ser adicionados à conta de usuário que executa o serviço, não à conta da máquina. Execute `setspn -S HTTP/myservername.mydomain.com myuser` em um shell de comando administrativo.

### <a name="linux-and-macos-environment-configuration"></a>Configuração de ambiente Linux e macOS

As instruções para ingressar em um computador Linux ou macOS em um domínio do Windows estão disponíveis no artigo [conectar Azure Data Studio ao seu SQL Server usando a autenticação do Windows-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) . As instruções criam uma conta de computador para o computador Linux no domínio. Os SPNs devem ser adicionados a essa conta de computador.

> [!NOTE]
> Ao seguir as diretrizes no artigo [conectar Azure Data Studio ao seu SQL Server usando a autenticação do Windows-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) , substitua `python-software-properties` por `python3-software-properties` se necessário.

Depois que o computador Linux ou macOS for ingressado no domínio, serão necessárias etapas adicionais para fornecer um [arquivo keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) com os SPNs:

* No controlador de domínio, adicione novos SPNs de serviço Web à conta do computador:
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* Use [ktpass](/windows-server/administration/windows-commands/ktpass) para gerar um arquivo keytab:
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * Alguns campos devem ser especificados em letras maiúsculas, conforme indicado.
* Copie o arquivo keytab para o computador Linux ou macOS.
* Selecione o arquivo keytab por meio de uma variável de ambiente:`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`
* Invocar `klist` para mostrar os SPNs disponíveis no momento para uso.

> [!NOTE]
> Um arquivo keytab contém credenciais de acesso ao domínio e deve ser protegido de acordo.

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

O [HTTP.sys](xref:fundamentals/servers/httpsys) dá suporte à autenticação do Windows no modo kernel usando a autenticação Negotiate, NTLM ou básica.

Adicione serviços de autenticação invocando <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) em `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

Configure o host da Web do aplicativo para usar HTTP.sys com a autenticação do Windows (*Program.cs*). <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>está no <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> O HTTP.sys delega à autenticação de modo kernel com o protocolo de autenticação Kerberos. Não há suporte para autenticação de modo de usuário com o Kerberos e o HTTP.sys. A conta do computador precisa ser usada para descriptografar o token/tíquete do Kerberos que é obtido do Active Directory e encaminhado pelo cliente ao servidor para autenticar o usuário. Registre o SPN (nome da entidade de serviço) do host, não do usuário do aplicativo.

> [!NOTE]
> Não há suporte para HTTP.sys no nano Server versão 1709 ou posterior. Para usar a autenticação do Windows e HTTP.sys com o nano Server, use um [contêiner do Server Core (Microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/). Para obter mais informações sobre o Server Core, consulte [o que é a opção de instalação do Server Core no Windows Server?](/windows-server/administration/server-core/what-is-server-core).

## <a name="authorize-users"></a>Autorizar usuários

O estado de configuração do acesso anônimo determina a maneira como os `[Authorize]` `[AllowAnonymous]` atributos e são usados no aplicativo. As duas seções a seguir explicam como lidar com os Estados de configuração não permitido e permitidos de acesso anônimo.

### <a name="disallow-anonymous-access"></a>Não permitir acesso anônimo

Quando a autenticação do Windows estiver habilitada e o acesso anônimo estiver desabilitado, os `[Authorize]` `[AllowAnonymous]` atributos e não terão nenhum efeito. Se um site do IIS estiver configurado para não permitir acesso anônimo, a solicitação nunca atingirá o aplicativo. Por esse motivo, o `[AllowAnonymous]` atributo não é aplicável.

### <a name="allow-anonymous-access"></a>Permitir acesso anônimo

Quando a autenticação do Windows e o acesso anônimo estiverem habilitados, use os `[Authorize]` `[AllowAnonymous]` atributos e. O `[Authorize]` atributo permite que você proteja pontos de extremidade do aplicativo que exigem autenticação. O `[AllowAnonymous]` atributo substitui o `[Authorize]` atributo em aplicativos que permitem acesso anônimo. Para obter detalhes de uso do atributo, consulte <xref:security/authorization/simple> .

> [!NOTE]
> Por padrão, os usuários que não têm autorização para acessar uma página são apresentados com uma resposta HTTP 403 vazia. O [middleware StatusCodePages](xref:fundamentals/error-handling#usestatuscodepages) pode ser configurado para fornecer aos usuários uma experiência de "acesso negado" melhor.

## <a name="impersonation"></a>Representação

ASP.NET Core não implementa a representação. Os aplicativos são executados com a identidade do aplicativo para todas as solicitações, usando o pool de aplicativos ou a identidade do processo. Se o aplicativo deve executar uma ação em nome de um usuário, use [WindowsIdentity. RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) em um [middleware embutido de terminal](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) no `Startup.Configure` . Execute uma única ação neste contexto e, em seguida, feche o contexto.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated`o não oferece suporte a operações assíncronas e não deve ser usado para cenários complexos. Por exemplo, o encapsulamento de solicitações inteiras ou cadeias de middleware não tem suporte ou é recomendado.

::: moniker range=">= aspnetcore-3.0"

Embora o pacote [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) habilite a autenticação no Windows, no Linux e no MacOS, há suporte para a representação apenas no Windows.

::: moniker-end

## <a name="claims-transformations"></a>Transformações de declarações

::: moniker range=">= aspnetcore-3.0"

Ao hospedar com o IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário. Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão. Para obter mais informações e um exemplo de código que ativa as transformações de declarações, consulte <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Ao hospedar com o IIS no modo de processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> o não é chamado internamente para inicializar um usuário. Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão. Para obter mais informações e um exemplo de código que ativa transformações de declarações ao hospedar em processo, consulte <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
