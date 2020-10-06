---
title: Hospedagem em processo com IIS e ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a hospedagem em processo com o IIS e o módulo ASP.NET Core.
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: ecf873e6ad2044aae87a5e7dc07316eae0e10912
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755226"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a>Hospedagem em processo com IIS e ASP.NET Core 

A hospedagem em processo executa um aplicativo ASP.NET Core no mesmo processo que o processo de trabalho do IIS. A hospedagem em processo oferece desempenho melhor em hospedagem fora do processo porque as solicitações não são transmitidas por proxy pelo adaptador de loopback, um adaptador de rede que retorna o tráfego de rede de saída para o mesmo computador.

O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado em processo:

![Módulo do ASP.NET Core no cenário de hospedagem em processo](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a>Habilitar hospedagem em processo

Desde ASP.NET Core 3,0, a hospedagem em processo foi habilitada por padrão para todos os aplicativos implantados no IIS.

Para configurar explicitamente um aplicativo para hospedagem em processo, defina o valor da `<AspNetCoreHostingModel>` propriedade como `InProcess` no arquivo de projeto ( `.csproj` ):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a>Arquitetura geral

O fluxo geral de uma solicitação é o seguinte:

1. A solicitação chega da Web para o driver do HTTP.sys no modo kernel.
1. O driver roteia as solicitações nativas ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).
1. O módulo ASP.NET Core recebe a solicitação nativa e a passa para o servidor HTTP IIS ( `IISHttpServer` ). O servidor HTTP do IIS é uma implementação de servidor em processo do IIS que converte a solicitação de nativa para gerenciada.

Depois que o servidor HTTP IIS processar a solicitação:

1. A solicitação é enviada para o pipeline de middleware ASP.NET Core.
1. O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.
1. A resposta do aplicativo é retornada ao IIS por meio do Servidor HTTP do IIS.
1. O IIS enviará a resposta ao cliente que iniciou a solicitação.

`CreateDefaultBuilder` Adiciona uma <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instância chamando o <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> método para inicializar o [CoreCLR](/dotnet/standard/glossary#coreclr) e hospedar o aplicativo dentro do processo de trabalho do IIS ( `w3wp.exe` ou `iisexpress.exe` ). Os testes de desempenho indicam que hospedar um aplicativo em processo do .NET Core oferece uma de taxa de transferência de solicitação significativamente mais elevada em comparação a hospedar o aplicativo fora do processo e enviar por proxy as solicitações para o [Kestrel](xref:fundamentals/servers/kestrel).

Aplicativos publicados como um único arquivo executável não podem ser carregados pelo modelo de hospedagem em processo.

## <a name="application-configuration"></a>Configuração de aplicativo

Para configurar opções do IIS, inclua uma configuração de serviço para <xref:Microsoft.AspNetCore.Builder.IISServerOptions> em <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>. O exemplo a seguir desabilita AutomaticAuthentication:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Opção | Padrão | Configuração |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | Se `true`, o Servidor do IIS define o `HttpContext.User` autenticado pela [Autenticação do Windows](xref:security/authentication/windowsauth). Se `false`, o servidor fornecerá apenas uma identidade para `HttpContext.User` e responderá a desafios quando explicitamente solicitado pelo `AuthenticationScheme`. A autenticação do Windows deve estar habilitada no IIS para que o `AutomaticAuthentication` funcione. Para obter mais informações, veja [Autenticação do Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName` | `null` | Configura o nome de exibição mostrado aos usuários em páginas de logon. |
| `AllowSynchronousIO` | `false` | Se a e/s síncrona é permitida para o `HttpContext.Request` e o `HttpContext.Response` . |
| `MaxRequestBodySize` | `30000000` | Obtém ou define o tamanho máximo do corpo da solicitação para o `HttpRequest`. Observe que o próprio IIS tem o limite `maxAllowedContentLength` que será processado antes de `MaxRequestBodySize` definido no `IISServerOptions`. Alterar `MaxRequestBodySize` não afetará `maxAllowedContentLength`. Para aumentar `maxAllowedContentLength` , adicione uma entrada no `web.config` para definir `maxAllowedContentLength` como um valor mais alto. Para obter mais detalhes, confira [Configuração](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a>Diferenças entre a hospedagem em processo e fora do processo

As seguintes características se aplicam ao hospedar em processo:

* O Servidor HTTP do IIS (`IISHttpServer`) é usado, em vez do servidor [Kestrel](xref:fundamentals/servers/kestrel). Para o em processo, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> para:

  * Registrar o `IISHttpServer`.
  * Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.
  * Configurar o host para capturar erros de inicialização.

* O [ `requestTimeout` atributo](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) não se aplica à hospedagem em processo.

* Não há suporte para o compartilhamento do pool de aplicativos entre aplicativos. Use um pool de aplicativos por aplicativo.

* A arquitetura (número de bit) do aplicativo e o runtime instalado (x64 ou x86) devem corresponder à arquitetura do pool de aplicativos. Por exemplo, os aplicativos publicados para 32 bits (x86) devem ter 32 bits habilitado para seus pools de aplicativos do IIS. Para obter mais informações, consulte a seção [criar o site do IIS](xref:tutorials/publish-to-iis#create-the-iis-site) .

* As desconexões do cliente são detectadas. O [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) token de cancelamento é cancelado quando o cliente se desconecta.

* Ao hospedar em processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> não é chamado internamente para inicializar um usuário. Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão. Quando a transformação de declarações com uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chame <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> para adicionar serviços de autenticação:

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
  
* Não há suporte para [implantações de pacote da Web (arquivo único)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) .
