---
title: Hospedagem fora do processo com IIS e ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a hospedagem fora do processo com o IIS e o módulo ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060411"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a>Hospedagem fora do processo com IIS e ASP.NET Core 

Como ASP.NET Core aplicativos são executados em um processo separado do processo de trabalho do IIS, o módulo ASP.NET Core lida com o gerenciamento de processos. O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha. Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado de fora d processo:

![Módulo do ASP.NET Core no cenário de hospedagem fora do processo](index/_static/ancm-outofprocess.png)

1. As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.
1. O driver roteia as solicitações para o IIS na porta configurada do site. A porta configurada geralmente é 80 (HTTP) ou 443 (HTTPS).
1. O módulo encaminha as solicitações para Kestrel em uma porta aleatória para o aplicativo. A porta aleatória não é 80 ou 443.

<!-- make this a bullet list -->
O módulo ASP.NET Core especifica a porta por meio de uma variável de ambiente na inicialização. A <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extensão configura o servidor a ser escutado `http://localhost:{PORT}` . Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas. O módulo não dá suporte ao encaminhamento de HTTPS. As solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS sobre HTTPS.

Depois que o Kestrel pega a solicitação do módulo, a solicitação é encaminhada para o pipeline de middleware ASP.NET Core. O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo. O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel. A resposta do aplicativo é passada de volta para o IIS, que o encaminha de volta para o cliente HTTP que iniciou a solicitação.

Para orientação sobre a configuração do Módulo do ASP.NET Core, consulte <xref:host-and-deploy/aspnet-core-module>.

Para saber mais sobre hospedagem, confira [Host no ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Configuração de aplicativo

### <a name="enable-the-iisintegration-components"></a>Habilitar os componentes de IISIntegration

Ao criar um host no `CreateHostBuilder` ( `Program.cs` ), chame <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> para habilitar a integração do IIS:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Para obter mais informações sobre o `CreateDefaultBuilder`, consulte <xref:fundamentals/host/generic-host#default-builder-settings>.


**Modelo de hospedagem de fora do processo**

Para configurar opções do IIS, inclua uma configuração de serviço para <xref:Microsoft.AspNetCore.Builder.IISOptions> em <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>. O exemplo a seguir impede que o aplicativo preencha `HttpContext.Connection.ClientCertificate`:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Opção                         | Padrão | Configuração |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Se `true`, o [middleware de integração do IIS](#enable-the-iisintegration-components) define o `HttpContext.User` autenticado pela [Autenticação do Windows](xref:security/authentication/windowsauth). Se `false`, o middleware fornecerá apenas uma identidade para `HttpContext.User` e responderá a desafios quando explicitamente solicitado pelo `AuthenticationScheme`. A autenticação do Windows deve estar habilitada no IIS para que o `AutomaticAuthentication` funcione. Saiba mais no tópico [Autenticação do Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Configura o nome de exibição mostrado aos usuários em páginas de logon. |
| `ForwardClientCertificate`     | `true`  | Se `true` e o cabeçalho da solicitação `MS-ASPNETCORE-CLIENTCERT` estiverem presentes, o `HttpContext.Connection.ClientCertificate` será populado. |


### <a name="proxy-server-and-load-balancer-scenarios"></a>Servidor proxy e cenários de balanceador de carga

O [middleware de integração do IIS](#enable-the-iisintegration-components) e o módulo ASP.NET Core são configurados para encaminhar o:

* Esquema (HTTP/HTTPS).
* Endereço IP remoto em que a solicitação foi originada.

O [middleware de integração do IIS](#enable-the-iisintegration-components) configura o middleware de cabeçalhos encaminhados.

Configuração adicional pode ser necessária para aplicativos hospedados atrás de servidores proxy adicionais e balanceadores de carga. Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).


### <a name="out-of-process-hosting-model"></a>Modelo de hospedagem de fora do processo

Para configurar um aplicativo para hospedagem fora do processo, defina o valor da `<AspNetCoreHostingModel>` propriedade como `OutOfProcess` no arquivo de projeto ( `.csproj` ):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

A hospedagem em processo é definida com `InProcess` , que é o valor padrão.

O valor de `<AspNetCoreHostingModel>` não diferencia maiúsculas de minúsculas, portanto, `inprocess` e `outofprocess` são valores válidos.

O servidor [Kestrel](xref:fundamentals/servers/kestrel) é usado, em vez do servidor HTTP do IIS (`IISHttpServer`).

Para o fora do processo, o [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> para:

* Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.
* Configurar o host para capturar erros de inicialização.

### <a name="process-name"></a>Nome do processo

`Process.GetCurrentProcess().ProcessName` relata `w3wp`/`iisexpress` (em processo) ou `dotnet` (fora do processo).

Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos. Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.

O Módulo do ASP.NET Core também pode:

* Definir variáveis de ambiente para o processo de trabalho.
* Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.
* Encaminhar tokens de autenticação do Windows.
