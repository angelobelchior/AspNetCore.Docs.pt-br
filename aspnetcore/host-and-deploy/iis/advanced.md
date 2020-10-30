---
title: Configuração avançada
author: rick-anderson
description: Configuração avançada com o módulo ASP.NET Core e o Serviços de Informações da Internet (IIS).
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
no-loc:
- appsettings.json
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: 9f14929a7d298d6f4d66abcc88665db34fc072bf
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058604"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a>Configuração avançada do módulo ASP.NET Core e do IIS

Este artigo aborda opções de configuração avançadas e cenários para o módulo ASP.NET Core e o IIS.

## <a name="modify-the-stack-size"></a>Modificar o tamanho da pilha

*Aplica-se somente ao usar o modelo de hospedagem em processo.*

Configure o tamanho da pilha gerenciada usando a `stackSize` configuração em bytes no `web.config` arquivo. O tamanho padrão é 1.048.576 bytes (1 MB). O exemplo a seguir altera o tamanho da pilha para 2 MB (2.097.152 bytes):

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>A configuração de proxy usa o protocolo HTTP e um token de emparelhamento

*Só se aplica à hospedagem de fora do processo.*

O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP. Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.

Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem. O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo. O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy. O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente. Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada. A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor. Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>Módulo do ASP.NET Core com uma configuração do IIS compartilhada

O instalador do módulo ASP.NET Core é executado com os privilégios da `TrustedInstaller` conta. Como a conta do sistema local não tem permissão Modificar para o caminho de compartilhamento usado pela configuração compartilhada do IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações do módulo no `applicationHost.config` arquivo no compartilhamento.

Ao usar uma configuração compartilhada de IIS no mesmo computador que a instalação do IIS, execute o instalador do pacote de hospedagem do ASP.NET Core com o parâmetro `OPT_NO_SHARED_CONFIG_CHECK` definido como `1`:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Quando o caminho para a configuração compartilhada não está no mesmo computador que a instalação do IIS, siga estas etapas:

1. Desabilite a configuração compartilhada de IIS.
1. Execute o instalador.
1. Exporte o `applicationHost.config` arquivo atualizado para o compartilhamento de arquivos.
1. Reabilite a Configuração Compartilhada do IIS.

## <a name="data-protection"></a>Proteção de dados

A [pilha Proteção de Dados do ASP.NET Core](xref:security/data-protection/introduction) é usada por vários [middlewares](xref:fundamentals/middleware/index) ASP.NET Core, incluindo aqueles usados na autenticação. Mesmo se as APIs de proteção de dados não forem chamadas pelo código do usuário, a proteção de dados deverá ser configurada com um script de implantação ou no código do usuário para criar um [repositório de chaves](xref:security/data-protection/implementation/key-management) criptográfico persistente. Se a proteção de dados não estiver configurada, as chaves serão mantidas na memória e descartadas quando o aplicativo for reiniciado.

Se o anel da chave de proteção de dados for armazenado na memória quando o aplicativo for reiniciado:

* Os cookie tokens de autenticação baseados em todos os são invalidados. 
* Os usuários precisam entrar novamente na próxima solicitação deles. 
* Todos os dados protegidos com o token de autenticação não poderão mais ser descriptografados. Isso pode incluir [tokens CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e [ASP.NET Core cookie s de TempData do MVC](xref:fundamentals/app-state#tempdata).

Para configurar a proteção de dados no IIS para persistir o token de autenticação, use **uma** das seguintes abordagens:

* **Criar chaves do registro de proteção de dados**

  As chaves de proteção de dados usadas por ASP.NET Core aplicativos são armazenadas no registro externo para os aplicativos. Para manter as chaves de um determinado aplicativo, crie chaves do registro para o pool de aplicativos.

  Para instalações autônomas do IIS não Web Farm, você pode usar o [script Provision-AutoGenKeys.ps1 de Proteção de Dados do PowerShell](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) para cada pool de aplicativos usado com um aplicativo ASP.NET Core. Esse script cria uma chave do registro no registro HKLM que é acessível somente para a conta de processo de trabalho do pool de aplicativos do aplicativo. As chaves são criptografadas em repouso usando a DPAPI com uma chave para todo o computador.

  Em cenários de web farm, um aplicativo pode ser configurado para usar um caminho UNC para armazenar seu anel de chave de proteção de dados. Por padrão, as chaves não são criptografadas. Verifique se as permissões de arquivo para o compartilhamento de rede estão limitadas à conta do Windows em que o aplicativo é executado. Um certificado X509 pode ser usado para proteger chaves em repouso. Considere um mecanismo para permitir que os usuários carreguem certificados. coloque os certificados no repositório de certificados confiáveis do usuário e certifique-se de que eles estejam disponíveis em todos os computadores nos quais o aplicativo do usuário é executado. Para obter mais informações, consulte <xref:security/data-protection/configuration/overview>.

* **Configurar o pool de aplicativos do IIS para carregar o perfil do usuário**

  Essa configuração está na seção **Modelo de processo** nas **Configurações avançadas** do pool de aplicativos. Defina **carregar perfil do usuário** como `True` . Quando definido como `True`, as chaves são armazenadas no diretório do perfil do usuário e protegidas usando DPAPI com uma chave específica para a conta de usuário. As chaves são mantidas na `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` pasta.

  O [ `setProfileEnvironment` atributo](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) do pool de aplicativos também deve ser habilitado. O valor padrão de `setProfileEnvironment` é `true`. Em alguns cenários (por exemplo, um SO Windows), `setProfileEnvironment` é definido como `false`. Se as chaves não estiverem armazenadas no diretório do perfil do usuário como esperado:

  1. Navegue até a pasta `%windir%/system32/inetsrv/config`.
  1. Abra o arquivo `applicationHost.config` .
  1. Localize o elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Confirme se o atributo `setProfileEnvironment` não está presente, que tem como padrão o valor `true`, ou defina explicitamente o valor do atributo como `true`.

* **Usar o sistema de arquivos como um repositório de tokens de autenticação**

  Ajuste o código do aplicativo para [usar o sistema de arquivos como um repositório de tokens de autenticação](xref:security/data-protection/configuration/overview). Use um certificado X509 para proteger o token de autenticação e verifique se ele é um certificado confiável. Se o certificado for autoassinado, você deverá colocá-lo no repositório Raiz confiável.

  Ao usar o IIS em uma web farm:

  * Use um compartilhamento de arquivos que pode ser acessado por todos os computadores.
  * Implante um certificado X509 em cada computador. Configure a [proteção de dados no código](xref:security/data-protection/configuration/overview).

* **Definir uma política de todo o computador para proteção de dados**

  O sistema de proteção de dados tem suporte limitado para definir uma [política padrão em todo o computador](xref:security/data-protection/configuration/machine-wide-policy) para todos os aplicativos que consomem as APIs de proteção de dados. Para obter mais informações, consulte <xref:security/data-protection/introduction>.

## <a name="iis-configuration"></a>Configuração do IIS

**Sistemas operacionais do Windows Server**

Habilite a função **Servidor Web (IIS)** e estabeleça serviços de função.

1. Use o assistente **Adicionar Funções e Recursos** por meio do menu **Gerenciar** ou do link no **Gerenciador do Servidor** . Na etapa **Funções de Servidor** , marque a caixa de **Servidor Web (IIS)** .

   ![A função de Servidor Web IIS é selecionada na etapa Selecionar funções de servidor.](index/_static/server-roles-ws2016.png)

1. Após a etapa **Recursos** , a etapa **Serviços de função** é carregada para o servidor Web (IIS). Selecione os serviços de função do IIS desejados ou aceite os serviços de função padrão fornecidos.

   ![Os serviços de função padrão são selecionados na etapa Selecionar serviços de função.](index/_static/role-services-ws2016.png)

   **Autenticação do Windows (opcional)**  
   Para habilitar a autenticação do Windows, expanda os seguintes nós: segurança do **servidor Web**  >  **Security** . Selecione o recurso **Autenticação do Windows** . Para obter mais informações, consulte [autenticação `<windowsAuthentication>` do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [Configurar a autenticação do Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcional)**  
   O WebSockets é compatível com o ASP.NET Core 1.1 ou posterior. Para habilitar WebSockets, expanda os seguintes nós: desenvolvimento de aplicativo de **servidor Web**  >  **Application Development** . Selecione o recurso **Protocolo WebSocket** . Para obter mais informações, consulte [WebSockets](xref:fundamentals/websockets).

1. Continue para a etapa **Confirmação** para instalar os serviços e a função de servidor Web. Uma reinicialização de servidor/IIS não é necessária após a instalação da função do **servidor Web (IIS)** .

**Sistemas operacionais de área de trabalho do Windows**

Habilite o **Console de Gerenciamento do IIS** e os **Serviços na World Wide Web** .

1. Navegue até o **painel de controle**  >  **programas**  >  **programas e recursos**  >  **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).

1. Abra o nó **Serviços de Informações da Internet** . Abra o nó **Ferramentas de Gerenciamento da Web** .

1. Marque a caixa de **Console de Gerenciamento do IIS** .

1. Marque a caixa de **Serviços na World Wide Web** .

1. Aceite os recursos padrão dos **Serviços na World Wide Web** ou personalize os recursos do IIS.

   **Autenticação do Windows (opcional)**  
   Para habilitar a autenticação do Windows, expanda os seguintes nós: segurança **dos serviços de World Wide Web**  >  **Security** . Selecione o recurso **Autenticação do Windows** . Para obter mais informações, consulte [autenticação `<windowsAuthentication>` do Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [Configurar a autenticação do Windows](xref:security/authentication/windowsauth).

   **WebSockets (opcional)**  
   O WebSockets é compatível com o ASP.NET Core 1.1 ou posterior. Para habilitar WebSockets, expanda os seguintes nós: recursos de desenvolvimento de aplicativos **World Wide Web Services**  >  **Application Development Features** . Selecione o recurso **Protocolo WebSocket** . Para obter mais informações, consulte [WebSockets](xref:fundamentals/websockets).

1. Se a instalação do IIS exigir uma reinicialização, reinicie o sistema.

![O Console de Gerenciamento do IIS e os Serviços na World Wide Web estão selecionados em Recursos do Windows.](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a>Diretórios virtuais

[Diretórios virtuais IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) não são compatíveis com aplicativos ASP.NET Core. Um aplicativo pode ser hospedado como um [subaplicativo](#sub-applications).

## <a name="sub-applications"></a>Subaplicativos

Um aplicativo ASP.NET Core pode ser hospedado como um [subaplicativo IIS (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). A parte do caminho do subaplicativo se torna parte da URL raiz do aplicativo.

Links de ativos estáticos dentro do subaplicativo devem usar a notação de sinal de til e barra (`~/`). A notação de sinal de til e barra aciona um [Auxiliar de Marca](xref:mvc/views/tag-helpers/intro) para preceder a base de caminho do subaplicativo ao link relativo renderizado. Para um subaplicativo no `/subapp_path`, uma imagem vinculada com `src="~/image.png"` é renderizada como `src="/subapp_path/image.png"`. O Middleware de Arquivo Estático do aplicativo raiz não processa a solicitação de arquivo estático. A solicitação é processada pelo Middleware de Arquivo Estático do subaplicativo.

Se um atributo de ativo estático `src` for definido como um caminho absoluto (por exemplo, `src="/image.png"`), o link será renderizado sem a base de caminho do subaplicativo. O Middleware de Arquivos Estáticos do aplicativo raiz tenta fornecer o ativo do [webroot](xref:fundamentals/index#web-root) da raiz do aplicativo, que resulta em uma resposta *404 – Não encontrado* , a menos que o ativo estático esteja disponível no aplicativo raiz.

Para hospedar um aplicativo ASP.NET Core como um subaplicativo em outro aplicativo do ASP.NET Core:

1. Estabeleça um pool de aplicativos para o subaplicativo. Defina a **versão do CLR do .NET** como **Sem Código Gerenciado** porque o Core Common Language Runtime (CoreCLR) do .NET Core é inicializado para hospedar o aplicativo no processo de trabalho, não no CLR de Área de trabalho (CLR do .NET).

1. Adicione o site raiz no Gerenciador do IIS com o subaplicativo em uma pasta no site raiz.

1. Clique com o botão direito do mouse na pasta do subaplicativo no Gerenciador do IIS e selecione **Converter em aplicativo** .

1. Na caixa de diálogo **Adicionar Aplicativo** , use o botão **Selecionar** no **Pool de Aplicativos** para atribuir o pool de aplicativos que você criou ao subaplicativo. Selecione **OK** .

A atribuição de um pool de aplicativos separado para o subaplicativo é um requisito ao usar o modelo de hospedagem em processo.

Para obter mais informações sobre o modelo de hospedagem em processo e como configurar o módulo ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module> .

## <a name="application-pools"></a>Pools de aplicativos

O isolamento do pool de aplicativos é determinado pelo modelo de hospedagem:

* Hospedagem em processo: os aplicativos devem ser executados em pools de aplicativos separados.
* Hospedagem fora do processo: é recomendável isolar os aplicativos uns dos outros executando cada aplicativo em seu próprio pool de aplicativos.

A caixa de diálogo **Adicionar Site** do IIS usa como padrão um único pool de aplicativos por aplicativo. Quando um **Nome de site** é fornecido, o texto é transferido automaticamente para a caixa de texto **Pool de aplicativos** . Um novo pool de aplicativos é criado usando o nome do site quando você adicionar o site.

## <a name="application-pool-no-locidentity"></a>Pool de aplicativos Identity

Uma conta de identidade do pool de aplicativos permite executar um aplicativo em uma conta exclusiva sem a necessidade de criar e gerenciar domínios ou contas locais. No IIS 8.0 ou posterior, o WAS (Processo de trabalho do administrador) do IIS cria uma conta virtual com o nome do novo pool de aplicativos e executa os processos de trabalho do pool de aplicativos nesta conta por padrão. No console de gerenciamento do IIS, em **Configurações avançadas** para o pool de aplicativos, verifique se o **Identity** está configurado para usar `ApplicationPoolIdentity` :

![Caixa de diálogo Configurações avançadas do pool de aplicativos](index/_static/apppool-identity.png)

O processo de gerenciamento do IIS cria um identificador seguro com o nome do pool de aplicativos no Sistema de segurança do Windows. Os recursos podem ser protegidos usando essa identidade. No entanto, essa identidade não é uma conta de usuário real e não será mostrada no Console de Gerenciamento de Usuários do Windows.

Se o processo de trabalho do IIS requerer acesso elevado ao aplicativo, modifique a ACL (lista de controle de acesso) do diretório que contém o aplicativo:

1. Abra o Windows Explorer e navegue para o diretório.

1. Clique com o botão direito do mouse no diretório e selecione **Propriedades** .

1. Na guia **Segurança** , selecione o botão **Editar** e, em seguida, no botão **Adicionar** .

1. Clique no botão **Locais** e verifique se o sistema está selecionado.

1. Insira o `IIS AppPool\{APP POOL NAME}` formato, em que o espaço reservado `{APP POOL NAME}` é o nome do pool de aplicativos, em **digite os nomes de objeto para selecionar a** área. Selecione o botão **Verificar Nomes** . Para o *DefaultAppPool* , verifique os nomes usando `IIS AppPool\DefaultAppPool` . Quando o botão **verificar nomes** é selecionado, um valor de `DefaultAppPool` é indicado na área nomes de objetos. Não é possível inserir o nome do pool de aplicativos diretamente na área de nomes de objeto. Use o `IIS AppPool\{APP POOL NAME}` formato, em que o espaço reservado `{APP POOL NAME}` é o nome do pool de aplicativos, ao verificar o nome do objeto.

   ![Selecione a caixa de diálogo de usuários ou grupos para a pasta do aplicativo: o nome do pool de aplicativos "DefaultAppPool" é anexado ao "IIS AppPool\" na área de nomes de objeto antes de selecionar"Verificar Nomes".](index/_static/select-users-or-groups-1.png)

1. Selecione **OK** .

   ![Selecione a caixa de diálogo de usuários ou grupos para a pasta do aplicativo: depois de selecionar "Verificar Nomes", o nome do objeto "DefaultAppPool" é mostrado na área de nomes de objeto.](index/_static/select-users-or-groups-2.png)

1. As permissões de leitura &amp; execução devem ser concedidas por padrão. Forneça permissões adicionais conforme necessário.

O acesso também pode ser concedido por meio de um prompt de comando usando a ferramenta **ICACLS** . Usando o *DefaultAppPool* como exemplo, o comando a seguir é usado:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Para saber mais, veja o tópico [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="http2-support"></a>Suporte do HTTP/2

O [HTTP/2](https://httpwg.org/specs/rfc7540.html) é compatível com ASP.NET Core nos seguintes cenários de implantação de IIS:

* Em processo
  * Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior
  * Conexão TLS 1.2 ou posterior
* Fora do processo
  * Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior
  * Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o [servidor Kestrel](xref:fundamentals/servers/kestrel) usa HTTP/1.1.
  * Conexão TLS 1.2 ou posterior

Para uma implantação em processo quando uma conexão HTTP/2 é estabelecida, os [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatórios `HTTP/2` . Para uma implantação fora do processo quando uma conexão HTTP/2 é estabelecida, os [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatórios `HTTP/1.1` .

Para saber mais sobre os modelos de hospedagem em processo e fora de processo, confira <xref:host-and-deploy/aspnet-core-module>.

O HTTP/2 está habilitado por padrão. As conexões retornarão para HTTP/1.1 se uma conexão HTTP/2 não for estabelecida. Para obter mais informações sobre a configuração de HTTP/2 com implantações do IIS, consulte [HTTP/2 no IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Solicitações de simulação do CORS

*Esta seção só se aplica a aplicativos ASP.NET Core com o .NET Framework como destino.*

Para um aplicativo ASP.NET Core com o .NET Framework como destino, as solicitações OPTIONS não são passadas para o aplicativo por padrão no IIS. Para saber como configurar os manipuladores do IIS do aplicativo no `web.config` para passar solicitações de opções, consulte [habilitar solicitações entre origens no ASP.NET Web API 2: como o CORS funciona](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Módulo de Inicialização de Aplicativo e Tempo Limite de Ociosidade

Quando hospedado no IIS pela versão 2 do Módulo do ASP.NET Core:

* [Módulo de inicialização do aplicativo](#application-initialization-module): hospedado [em processo](xref:host-and-deploy/iis/in-process-hosting) ou [fora do processo](xref:host-and-deploy/iis/out-of-process-hosting) do aplicativo pode ser configurado para iniciar automaticamente em uma reinicialização do processo de trabalho ou reinicialização do servidor.
* [Tempo limite de ociosidade](#idle-timeout): o aplicativo hospedado [no processo](xref:host-and-deploy/iis/in-process-hosting) pode ser configurado para não atingir o tempo limite durante períodos de inatividade.

### <a name="application-initialization-module"></a>Módulo de Inicialização de Aplicativo

*Aplica-se a aplicativos hospedados em processo e fora de processo.*

[Inicialização de Aplicativo IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) é um recurso do IIS que envia uma solicitação HTTP para o aplicativo quando o pool de aplicativos é iniciado ou reciclado. A solicitação dispara a inicialização do aplicativo. Por padrão, o IIS emite uma solicitação para a URL raiz do aplicativo (`/`) para inicializar o aplicativo (confira mais detalhes sobre a configuração nos [recursos adicionais](#application-initialization-module-and-idle-timeout-additional-resources)).

Confirme se o recurso da função Inicialização de Aplicativo do IIS está habilitado:

No Windows 7 ou sistemas de área de trabalho posteriores, ao usar o IIS localmente:

1. Navegue até o **painel de controle**  >  **programas**  >  **programas e recursos**  >  **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).
1. Abra **serviços de informações da Internet**  >  recursos de desenvolvimento de aplicativos **dos serviços World Wide Web**  >  **Application Development Features** .
1. Marque a caixa de seleção **Inicialização de Aplicativo** .

No Windows Server 2008 R2 ou posterior:

1. Abra o **Assistente de Adição de Funções e Recursos** .
1. No painel **Selecionar serviços de função** , abra o nó **Desenvolvimento de aplicativos** .
1. Marque a caixa de seleção **Inicialização de Aplicativo** .

Use quaisquer das abordagens a seguir para habilitar o Módulo de Inicialização do Aplicativo para o site:

* Usando o Gerenciador do IIS:

  1. Selecione **Pools de Aplicativos** no painel **Conexões** .
  1. Clique com o botão direito do mouse no pool de aplicativos do aplicativo na lista e selecione **Configurações Avançadas** .
  1. O **modo de início** padrão é `OnDemand` . Defina o **modo de início** como `AlwaysRunning` . Selecione **OK** .
  1. Abra o nó **Sites** no painel **Conexões** .
  1. Clique com o botão direito do mouse no aplicativo e selecione **gerenciar**  >  **Configurações avançadas** do site.
  1. A configuração de **pré-carregamento habilitada** padrão é `False` . Defina **pré-carregamento habilitado** para `True` . Selecione **OK** .

* Usando `web.config` , adicione o `<applicationInitialization>` elemento com `doAppInitAfterRestart` definido como `true` para os `<system.webServer>` elementos no arquivo do aplicativo `web.config` :

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a>Tempo limite de ociosidade

*Só se aplica a aplicativos hospedados em processo.*

Para impedir que o aplicativo fique ocioso, defina o tempo limite de ociosidade do pool de aplicativos, usando o Gerenciador do IIS:

1. Selecione **Pools de Aplicativos** no painel **Conexões** .
1. Clique com o botão direito do mouse no pool de aplicativos do aplicativo na lista e selecione **Configurações Avançadas** .
1. O **tempo limite de ociosidade padrão (minutos)** é `20` minutos. Defina o **tempo limite de ociosidade (minutos)** como `0` (zero). Selecione **OK** .
1. Recicle o processo de trabalho.

Para impedir que aplicativos hospedados [fora de processo](xref:host-and-deploy/iis/out-of-process-hosting) atinjam o tempo limite, use uma das seguintes abordagens:

* Execute ping do aplicativo de um serviço externo para mantê-lo em execução.
* Se o aplicativo hospedar apenas serviços em segundo plano, evite a hospedagem do IIS e use um [Serviço do Windows para hospedar o aplicativo ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Recursos adicionais do Módulo de Inicialização de Aplicativo e do Tempo Limite de Ociosidade

* [Inicialização de Aplicativo do IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Inicialização `<applicationInitialization>` do aplicativo ](/iis/configuration/system.webserver/applicationinitialization/).
* [Configurações do modelo de processo para um `<processModel>` pool de aplicativos ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="module-schema-and-configuration-file-locations"></a>Locais dos arquivos de módulo, de esquema e de configuração

### <a name="module"></a>Módulo

**IIS (x86/AMD64)** :

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express (x86/AMD64)** :

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a>Esquema

**IIS**

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

**IIS Express**

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a>Configuração

**IIS**

* `%windir%\System32\inetsrv\config\applicationHost.config`

**IIS Express**

* Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`

* *iisexpress.exe* CLI `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`

Os arquivos podem ser encontrados pesquisando `aspnetcore` no `applicationHost.config` arquivo.
