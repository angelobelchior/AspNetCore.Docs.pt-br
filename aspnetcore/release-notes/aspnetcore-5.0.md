---
title: O que há de novo no ASP.NET Core 5,0
author: rick-anderson
description: Saiba mais sobre os novos recursos do ASP.NET Core 5,0.
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
- Kestrel
uid: aspnetcore-5.0
ms.openlocfilehash: e25549d557dd971d0f2f4d67a182574f07138acb
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508117"
---
# <a name="whats-new-in-aspnet-core-50"></a>O que há de novo no ASP.NET Core 5,0

Este artigo destaca as alterações mais significativas no ASP.NET Core 5,0 com links para a documentação relevante.

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a>ASP.NET Core MVC e Razor melhorias

### <a name="model-binding-datetime-as-utc"></a>Data de associação de modelo como UTC

A associação de modelo agora dá suporte à associação de cadeias de caracteres de tempo UTC para `DateTime` Se a solicitação contiver uma cadeia de caracteres de hora UTC, a associação de modelo a associará a um UTC `DateTime` . Por exemplo, a seguinte cadeia de caracteres de tempo está associada ao UTC `DateTime` : `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`

### <a name="model-binding-and-validation-with-c-9-record-types"></a>Associação de modelo e validação com tipos de registro do C# 9

Os [tipos de registro do C# 9](/dotnet/csharp/whats-new/csharp-9#record-types) podem ser usados com a associação de modelo em um controlador MVC ou em uma Razor página. Os tipos de registro são uma boa maneira de modelar dados que estão sendo transmitidos pela rede.

Por exemplo, o seguinte `PersonController` usa o `Person` tipo de registro com associação de modelo e validação de formulário:

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

O arquivo `Person/Index.cshtml`:

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a>Melhorias no DynamicRouteValueTransformer

ASP.NET Core 3,1 foi introduzido <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> como uma maneira de usar o ponto de extremidade personalizado para selecionar dinamicamente uma ação do controlador MVC ou uma Razor página. ASP.NET Core aplicativos 5,0 podem passar o estado para um `DynamicRouteValueTransformer` e filtrar o conjunto de pontos de extremidade escolhido.

### <a name="miscellaneous"></a>Diversos

* O atributo [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) pode ser aplicado a propriedades em um Razor modelo de página.
* Os parâmetros e as propriedades associados do corpo são considerados obrigatórios por padrão. <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a>API Web

### <a name="openapi-specification-on-by-default"></a>Especificação de OpenAPI ativada por padrão

A [especificação de openapi](http://spec.openapis.org/oas/v3.0.3) é um padrão do setor para descrever as APIs http e integrá-las a processos comerciais complexos ou a terceiros. O OpenAPI é amplamente suportado por todos os provedores de nuvem e muitos registros de API. Os aplicativos que emitem OpenAPI documentos de APIs da Web têm uma variedade de novas oportunidades nas quais essas APIs podem ser usadas. Em parceria com os mantenedores do projeto de código-fonte aberto [swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), o modelo de API ASP.NET Core contém uma dependência do NuGet em [swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore). Swashbuckle é um popular pacote NuGet de software livre que emite documentos OpenAPI dinamicamente. Swashbuckle faz isso introspecção sobre os controladores de API e gerando o documento OpenAPI em tempo de execução, ou no momento da compilação usando a CLI do swashbuckle.

No ASP.NET Core 5,0, os modelos de API Web habilitam o suporte a OpenAPI por padrão. Para desabilitar o OpenAPI:

* Na linha de comando:

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* No Visual Studio: desmarque **habilitar suporte a openapi**.

Todos os arquivos *. csproj* criados para projetos de API Web contêm a referência de pacote NuGet [swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) .

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

O código gerado pelo modelo contém código `Startup.ConfigureServices` que ativa a geração de documentos openapi:

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

O `Startup.Configure` método adiciona o middleware Swashbuckle, que habilita o:

* Processo de geração de documentos.
* Página da interface do usuário do Swagger por padrão no modo de desenvolvimento.

O código gerado pelo modelo não expõe acidentalmente a descrição da API ao publicar em produção.

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a>Importação de gerenciamento de API do Azure

Quando ASP.NET Core projetos de API habilitam o OpenAPI, o Visual Studio 2019 versão 16,8 e a publicação posterior oferecem automaticamente uma etapa adicional no fluxo de publicação. Os desenvolvedores que usam o [Gerenciamento de API do Azure](xref:tutorials/publish-to-azure-api-management-using-vs) têm a oportunidade de importar automaticamente as APIs para o gerenciamento de API do Azure durante o fluxo de publicação:

![Importação do gerenciamento de API do Azure/publicação do VS](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a>Experiência de inicialização melhor para projetos de API Web

Com o OpenAPI habilitado por padrão, a experiência de inicialização do aplicativo (F5) para desenvolvedores de API Web melhora significativamente. Com o ASP.NET Core 5,0, o modelo de API da Web vem pré-configurado para carregar a página da interface do usuário do Swagger. A página da interface do usuário do Swagger fornece a documentação adicionada para a API publicada e permite testar as APIs com um único clique.

![exibição do Swagger/index.html](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a>Melhorias de desempenho

Para o .NET 5, fizemos melhorias significativas Blazor WebAssembly no desempenho do tempo de execução com um foco específico em processamento de interface do usuário e SERIALIZAÇÃO JSON complexos. Em nossos testes de desempenho, Blazor WebAssembly no .NET 5 é de duas a três vezes mais rápida para a maioria dos cenários. Para obter mais informações, consulte [blog do ASP.net: atualizações do ASP.NET Core no .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).

### <a name="css-isolation"></a>Isolamento de CSS

Blazor Agora dá suporte à definição de estilos CSS que têm o escopo de um determinado componente. Estilos CSS específicos de componentes facilitam o raciocínio sobre os estilos em um aplicativo e evitam efeitos colaterais não intencionais de estilos globais. Para obter mais informações, consulte <xref:blazor/components/css-isolation>.

### <a name="new-inputfile-component"></a>Novo `InputFile` componente

O `InputFile` componente permite ler um ou mais arquivos selecionados por um usuário para upload. Para obter mais informações, consulte <xref:blazor/file-uploads>.

### <a name="new-inputradio-and-inputradiogroup-components"></a>Novos `InputRadio` `InputRadioGroup` componentes e

Blazor tem `InputRadio` componentes internos e `InputRadioGroup` que simplificam a vinculação de dados a grupos de botões de opção com validação integrada. Para obter mais informações, consulte <xref:blazor/forms-validation>.

### <a name="component-virtualization"></a>Virtualização de componente

Melhore o desempenho percebido da renderização de componentes usando o Blazor suporte interno à virtualização da estrutura. Para obter mais informações, consulte <xref:blazor/forms-validation#radio-buttons>.

### <a name="ontoggle-event-support"></a>`ontoggle` suporte a eventos

Blazor os eventos agora dão suporte ao `ontoggle` evento dom. Para obter mais informações, consulte <xref:blazor/components/event-handling#event-argument-types>.

### <a name="set-ui-focus-in-no-locblazor-apps"></a>Definir foco da interface do usuário em Blazor aplicativos

Use o `FocusAsync` método de conveniência em referências de elemento para definir o foco da interface do usuário para esse elemento. Para obter mais informações, consulte <xref:blazor/components/event-handling#focus-an-element>.

### <a name="custom-validation-class-attributes"></a>Atributos da classe de validação personalizada

Os nomes de classe de validação personalizada são úteis na integração com estruturas CSS, como bootstrap. Para obter mais informações, consulte <xref:blazor/forms-validation#custom-validation-class-attributes>.

### <a name="iasyncdisposable-support"></a>Suporte do IAsyncDisposable

Blazor Agora, os componentes oferecem suporte à <xref:System.IAsyncDisposable> interface para a versão assíncrona de recursos alocados.

### <a name="javascript-isolation-and-object-references"></a>Isolamento de JavaScript e referências de objeto

Blazor habilita o isolamento de JavaScript em [módulos JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)padrão. Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

### <a name="form-components-support-display-name"></a>Os componentes do formulário dão suporte ao nome para exibição

Os seguintes componentes internos dão suporte à exibição de nomes com o `DisplayName` parâmetro:

* `InputDate`
* `InputNumber`
* `InputSelect`

Para obter mais informações, consulte <xref:blazor/forms-validation#display-name-support>.

### <a name="catch-all-route-parameters"></a>Capturar todos os parâmetros de rota

Catch-todos os parâmetros de rota, que capturam caminhos entre vários limites de pasta, têm suporte em componentes. Para obter mais informações, consulte <xref:blazor/fundamentals/routing#catch-all-route-parameters>.

### <a name="debugging-improvements"></a>Melhorias na depuração

Os Blazor WebAssembly aplicativos de depuração foram aprimorados no ASP.NET Core 5,0. Além disso, agora há suporte para a depuração no Visual Studio para Mac. Para obter mais informações, consulte <xref:blazor/debug>.

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a>Microsoft Identity v 2.0 e MSAL v 2.0

Blazor a segurança agora usa o Microsoft Identity v 2.0 ( [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) e o [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) ) e o MSAL v 2.0. Para obter mais informações, consulte os tópicos na [ Blazor segurança e no Identity nó](xref:blazor/security/index).

### <a name="protected-browser-storage-for-no-locblazor-server"></a>Armazenamento de navegador protegido para Blazor Server

Blazor Server Agora, os aplicativos podem usar o suporte interno para armazenar o estado do aplicativo no navegador que foi protegido contra violação usando a proteção de dados do ASP.NET Core. Os dados podem ser armazenados em armazenamento de navegador local ou de sessão. Para obter mais informações, consulte <xref:blazor/state-management>.

### <a name="no-locblazor-webassembly-prerendering"></a>Blazor WebAssembly prerenderizando

A integração de componentes é aprimorada em modelos de hospedagem, e os Blazor WebAssembly aplicativos agora podem sair da saída no servidor. <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a>Aprimoramentos de corte/vinculação

Blazor WebAssembly executa a filtragem/vinculação de IL (linguagem intermediária) durante uma compilação para cortar o IL desnecessário dos assemblies de saída do aplicativo. Com o lançamento do ASP.NET Core 5,0, o Blazor WebAssembly realiza uma remoção aprimorada com opções de configuração adicionais. Para obter mais informações, consulte <xref:blazor/host-and-deploy/configure-trimmer> e [Opções de corte](/dotnet/core/deploying/trimming-options).

### <a name="browser-compatibility-analyzer"></a>Analisador de compatibilidade do navegador

Blazor WebAssembly os aplicativos direcionam a área completa da superfície do .NET API, mas nem todas as APIs do .NET têm suporte no Webassembly devido a restrições de área restrita do navegador. APIs sem suporte são lançadas <xref:System.PlatformNotSupportedException> quando em execução no Webassembly. Um analisador de compatibilidade de plataforma avisa o desenvolvedor quando o aplicativo usa APIs que não são compatíveis com as plataformas de destino do aplicativo. Para obter mais informações, consulte <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.

### <a name="lazy-load-assemblies"></a>Assemblies de carga lenta

Blazor WebAssembly o desempenho de inicialização do aplicativo pode ser melhorado ao adiar o carregamento de alguns assemblies de aplicativo até que eles sejam necessários. Para obter mais informações, consulte <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="updated-globalization-support"></a>Suporte à globalização atualizado

O suporte à globalização está disponível para o Blazor WebAssembly com base em componentes internacionais para Unicode (ICU). Para obter mais informações, consulte <xref:blazor/globalization-localization>.

## <a name="grpc"></a>gRPC

Muitos aprimoramentos de preformabilidade foram feitos no [gRPC](https://grpc.io/). Para obter mais informações, consulte [melhorias de desempenho do gRPC no .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).

Para obter mais informações sobre o gRPC, consulte <xref:grpc/index> .

## SignalR

### <a name="no-locsignalr-hub-filters"></a>SignalR Filtros de Hub

SignalR Os filtros de Hub, chamados de pipelines de Hub no ASP.NET SignalR , são um recurso que permite que o código seja executado antes e depois que os métodos de Hub são chamados. A execução de código antes e depois dos métodos de Hub são chamados é semelhante a como o middleware tem a capacidade de executar código antes e depois de uma solicitação HTTP. Usos comuns incluem log, tratamento de erros e validação de argumento.

Para obter mais informações, consulte [usar filtros de Hub SignalR no ASP.NET Core ](xref:signalr/hub-filters).

### <a name="no-locsignalr-parallel-hub-invocations"></a>SignalR invocações de Hub paralelas

ASP.NET Core SignalR agora é capaz de lidar com invocações de Hub paralelas. O comportamento padrão pode ser alterado para permitir que os clientes invoquem mais de um método de Hub por vez:

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a>Adicionado suporte a MessagePack no SignalR cliente Java

Um novo pacote, [com. Microsoft. signalr. MessagePack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), adiciona suporte a MessagePack ao SignalR cliente Java. Para usar o protocolo Hub MessagePack, adicione `.withHubProtocol(new MessagePackHubProtocol())` ao construtor de conexões:

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* Pontos de extremidade recarregáveis por meio da configuração: Kestrel pode detectar alterações na configuração passada para [ KestrelServerOptions.Configlhar](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) e desassociar de pontos de extremidade existentes e associar a novos pontos de extremidade sem a necessidade de uma reinicialização do aplicativo quando o `reloadOnChange` parâmetro é `true` . Por padrão, ao usar <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> ou <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> , o é Kestrel associado à subseção de configuração [" Kestrel "](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) com `reloadOnChange` habilitado. Os aplicativos devem passar `reloadOnChange: true` ao chamar `KestrelServerOptions.Configure` manualmente para obter pontos de extremidade recarregáveis.
* Aprimoramentos de cabeçalhos de resposta HTTP/2. Para obter mais informações, consulte [melhorias de desempenho](#performance-improvements) na próxima seção.
* Suporte para tipos de pontos de extremidade adicionais no transporte de soquetes: adicionar à nova API introduzida no <xref:System.Net.Sockets?displayProperty=nameWithType> , o transporte padrão de soquetes no [Kestrel](xref:fundamentals/servers/kestrel) permite a ligação com os identificadores de arquivo existentes e os soquetes de domínio do UNIX. O suporte para associação a identificadores de arquivo existentes permite usar a `Systemd` integração existente sem exigir o `libuv` transporte.
* Decodificação de cabeçalho personalizado em Kestrel : os aplicativos podem especificar qual <xref:System.Text.Encoding> usar para interpretar cabeçalhos de entrada com base no nome do cabeçalho, em vez de padronizar para UTF-8. Defina o cabeçalho  <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> `Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` Propriedade para especificar qual codificação usar:
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a>Kestrel opções específicas de ponto de extremidade por meio da configuração

Foi adicionado suporte para a configuração de Kestrel opções específicas do ponto de extremidade por meio da [configuração](xref:fundamentals/configuration/index). As configurações específicas do ponto de extremidade incluem:

* Protocolos HTTP usados
* Protocolos TLS usados
* Certificado selecionado
* Modo de certificado cient

A configuração permite especificar qual certificado é selecionado com base no nome do servidor especificado. O nome do servidor faz parte da extensão de Indicação de Nome de Servidor (SNI) para o protocolo TLS, conforme indicado pelo cliente. Kestrela configuração do também dá suporte a um prefixo curinga no nome do host.

O exemplo a seguir mostra como especificar o ponto de extremidade específico usando um arquivo de configuração:

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

Indicação de Nome de Servidor (SNI) é uma extensão de TLS para incluir um domínio virtual como parte da negociação SSL. O que isso significa efetivamente é que o nome de domínio virtual ou um nome de host pode ser usado para identificar o ponto de extremidade de rede.

## <a name="performance-improvements"></a>Melhorias de desempenho

### <a name="http2"></a>HTTP/2

* Reduções significativas em alocações no caminho de código HTTP/2.
* Suporte para [compactação dinâmica HPack](https://tools.ietf.org/html/rfc7541) de cabeçalhos de resposta http/2 no [Kestrel](xref:fundamentals/servers/kestrel) . Para obter mais informações, consulte [tamanho da tabela de cabeçalho](xref:fundamentals/servers/kestrel#header-table-size) e [HPACK: a Killer (recurso) silenciosa do http/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).
* Enviando quadros de PING HTTP/2: o HTTP/2 tem um mecanismo para enviar quadros de PING para garantir que uma conexão ociosa ainda esteja funcional. Garantir uma conexão viável é especialmente útil ao trabalhar com fluxos de vida útil longa que geralmente estão ociosos, mas apenas verificar a atividade intermitentemente, por exemplo, fluxos de gRPC. Os aplicativos podem enviar quadros de PING periódicos no [Kestrel](xref:fundamentals/servers/kestrel) definindo limites em <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions> :

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a>Contêineres

Antes do .NET 5,0, a criação e a publicação de um *Dockerfile* para um aplicativo ASP.NET Core precisou extrair todo o SDK do .NET Core e a imagem ASP.NET Core. Com esta versão, a extração de bytes de imagens do SDK é reduzida e os bytes recebidos para a imagem de ASP.NET Core são amplamente eliminados. Para obter mais informações, consulte [este comentário sobre o problema do GitHub](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).

## <a name="authentication-and-authorization"></a>Autenticação e autorização

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a>Azure Active Directory autenticação com a Microsoft. Identity . Site

Os modelos de projeto ASP.NET Core agora se integram <xref:Microsoft.Identity.Web?displayProperty=fullName> ao para lidar com a autenticação com o Azure AD ( [diretório de atividades do Azure](/azure/active-directory/fundamentals/active-directory-whatis) ). O [Microsoft. Identity . O pacote da Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) fornece:

* Uma melhor experiência de autenticação por meio do Azure AD.
* Uma maneira mais fácil de acessar os recursos do Azure em nome de seus usuários, incluindo [Microsoft Graph](/graph/overview). Consulte o [Microsoft. Identity . Exemplo da Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), que começa com um logon básico e avança por meio de multilocação, usando as APIs do Azure, usando Microsoft Graph e protegendo suas próprias APIs. `Microsoft.Identity.Web` está disponível junto com o .NET 5.

### <a name="allow-anonymous-access-to-an-endpoint"></a>Permitir acesso anônimo a um ponto de extremidade

O `AllowAnonymous` método de extensão permite o acesso anônimo a um ponto de extremidade:

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a>Manipulação personalizada de falhas de autorização

A manipulação personalizada de falhas de autorização agora é mais fácil com a nova interface [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) que é invocada pelo [middleware](xref:fundamentals/middleware/index)de [autorização](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) . A implementação padrão permanece a mesma, mas um manipulador personalizado pode ser registrado em [injeção de dependência, que permite respostas HTTP personalizadas com base na razão pela qual a autorização falhou. Consulte [Este exemplo](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) que demonstra o uso do `IAuthorizationMiddlewareResultHandler` .

### <a name="authorization-when-using-endpoint-routing"></a>Autorização ao usar o roteamento de ponto de extremidade

A autorização ao usar o roteamento de ponto de extremidade agora recebe o `HttpContext` em vez da instância de ponto de extremidade. Isso permite que o middleware de autorização acesse o `RouteData` e outras propriedades do `HttpContext` que não estavam acessíveis por meio da <xref:Microsoft.AspNetCore.Http.Endpoint> classe. O ponto de extremidade pode ser buscado no contexto usando o [contexto. GetEndPoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a>Controle de acesso baseado em função com autenticação Kerberos e LDAP no Linux

Consulte [autenticação Kerberos e RBAC (controle de acesso baseado em função)](xref:security/authentication/windowsauth#rbac)

## <a name="api-improvements"></a>Aprimoramentos da API

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a>Métodos de extensão JSON para HttpRequest e HttpResponse

Os dados JSON podem ser lidos e gravados de um `HttpRequest` e `HttpResponse` usando os <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> métodos New e `WriteAsJsonAsync` extension. Esses métodos de extensão usam o [System.Text.Jsno](xref:System.Text.Json) serializador para manipular os dados JSON. O novo `HasJsonContentType` método de extensão também pode verificar se uma solicitação tem um tipo de conteúdo JSON.

Os métodos de extensão JSON podem ser combinados com o [Roteamento de ponto de extremidade](xref:fundamentals/routing) para criar APIs JSON em um estilo de programação que chamamos * **de rota para o código** _. É uma nova opção para os desenvolvedores que desejam criar APIs JSON básicas de forma leve. Por exemplo, um aplicativo Web que tem apenas alguns pontos de extremidade pode optar por usar a rota para código em vez da funcionalidade completa do ASP.NET Core MVC:

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

Para obter mais informações sobre os novos métodos de extensão JSON e rotear para código, consulte [este .net show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).

### <a name="systemdiagnosticsactivity"></a>System. Diagnostics. Activity

O formato padrão para <xref:System.Diagnostics.Activity?displayProperty=fullName> agora usa como padrão o formato W3C. Isso torna o suporte de rastreamento distribuído em ASP.NET Core interoperável com mais estruturas por padrão.

### <a name="frombodyattribute"></a>FromBodyAttribute

<xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> Agora dá suporte à configuração de uma opção que permite que esses parâmetros ou propriedades sejam considerados opcionais:

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                          MyModel model)
{
    ...
}
```

## <a name="miscellaneous-improvements"></a>Aprimoramentos diversos

Começamos a aplicar [anotações anuláveis](/dotnet/csharp/nullable-references#attributes-describe-apis) a ASP.NET Core assemblies. Planejamos anotar a maior parte da superfície da API pública comum da estrutura do .NET 5. <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a>Controlar a ativação da classe de inicialização

Uma sobrecarga adicional foi <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> adicionada para permitir que um aplicativo forneça um método de fábrica para controlar a `Startup` ativação de classe. Controlar a `Startup` ativação de classe é útil para passar parâmetros adicionais para `Startup` o que são inicializados junto com o host:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a>Atualização automática com a inspeção dotnet

No .NET 5, a execução do [dotnet Watch](xref:tutorials/dotnet-watch) em um projeto ASP.NET Core inicia o navegador padrão e atualiza automaticamente o navegador conforme as alterações são feitas no código. Isso significa que você pode:

_ Abra um projeto de ASP.NET Core em um editor de texto.
* Execute `dotnet watch`.
* Concentre-se nas alterações de código enquanto as ferramentas lidam com a recriação, reinicialização e recarregamento do aplicativo.

Esperamos trazer a funcionalidade de atualização automática para o Visual Studio no futuro.

### <a name="console-logger-formatter"></a>Formatador de agente do console

Foram feitas melhorias no provedor de log do console na `Microsoft.Extensions.Logging` biblioteca. Agora, os desenvolvedores podem implementar um personalizado `ConsoleFormatter` para exercer controle completo sobre a formatação e a colorização da saída do console. As APIs do formatador permitem formatação avançada implementando um subconjunto das sequências de escape VT-100. O VT-100 tem suporte da maioria dos terminais modernos. O agente de log do console pode analisar as sequências de escape em terminais sem suporte, permitindo que os desenvolvedores criem um único formatador para todos os terminais.

### <a name="json-console-logger"></a>Agente do console JSON

Além de dar suporte a formatadores personalizados, também adicionamos um formatador JSON interno que emite logs estruturados de JSON ao console. O código a seguir mostra como alternar do agente de log padrão para JSON:

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

As mensagens de log emitidas para o console são formatadas como JSON:

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
