---
title: Hospedar e implantar ASP.NET Core Blazor WebAssembly
author: guardrex
description: Saiba como hospedar e implantar um Blazor aplicativo usando ASP.NET Core, CDN (redes de distribuição de conteúdo), servidores de arquivos e páginas do github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: dadf6076e7f07c07381856aa225667a6eb38046a
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080310"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a>Hospedar e implantar ASP.NET Core Blazor WebAssembly

Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)e [Safia Abdalla](https://safia.rocks)

Com o [ Blazor WebAssembly modelo de hospedagem](xref:blazor/hosting-models#blazor-webassembly):

* O Blazor aplicativo, suas dependências e o tempo de execução do .NET são baixados para o navegador em paralelo.
* O aplicativo é executado diretamente no thread da interface do usuário do navegador.

Há suporte para as seguintes estratégias de implantação:

* O Blazor aplicativo é servido por um aplicativo ASP.NET Core. Esta estratégia é abordada na seção [Implantação hospedada com o ASP.NET Core](#hosted-deployment-with-aspnet-core).
* O Blazor aplicativo é colocado em um servidor Web ou serviço de hospedagem estática, em que o .net não é usado para servir o Blazor aplicativo. Essa estratégia é abordada na seção [implantação autônoma](#standalone-deployment) , que inclui informações sobre como hospedar um Blazor WebAssembly aplicativo como um subaplicativo do IIS.

## <a name="compression"></a>Compactação

Quando um Blazor WebAssembly aplicativo é publicado, a saída é compactada estaticamente durante a publicação para reduzir o tamanho do aplicativo e remover a sobrecarga de compactação de tempo de execução. Os seguintes algoritmos de compactação são usados:

* [Brotli](https://tools.ietf.org/html/rfc7932) (nível mais alto)
* [Gzip](https://tools.ietf.org/html/rfc1952)

Blazor o se baseia no host para o fornecer os arquivos compactados apropriados. Ao usar um projeto ASP.NET Core hospedado, o projeto host é capaz de executar a negociação de conteúdo e fornecer os arquivos compactados estaticamente. Ao hospedar um Blazor WebAssembly aplicativo autônomo, um trabalho adicional pode ser necessário para garantir que arquivos compactados estaticamente sejam atendidos:

* Para `web.config` a configuração de compactação do IIS, consulte a seção [IIS: Brotli e a compactação Gzip](#brotli-and-gzip-compression) . 
* Ao hospedar soluções de hospedagem estática que não dão suporte à negociação de conteúdo de arquivo compactado estaticamente, como páginas do GitHub, considere configurar o aplicativo para buscar e decodificar arquivos compactados Brotli:

  * Obtenha o decodificador Brotli do JavaScript do [repositório GitHub do Google/Brotli](https://github.com/google/brotli). A partir de julho de 2020, o arquivo de decodificador é nomeado `decode.min.js` e encontrado na [ `js` pasta](https://github.com/google/brotli/tree/master/js)do repositório.
  * Atualize o aplicativo para usar o decodificador. Altere a marcação dentro da `<body>` marca de fechamento `wwwroot/index.html` para o seguinte:
  
    ```html
    <script src="decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
Para desabilitar a compactação, adicione a `BlazorEnableCompression` Propriedade MSBuild ao arquivo de projeto do aplicativo e defina o valor como `false` :

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

A `BlazorEnableCompression` propriedade pode ser passada para o [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando com a seguinte sintaxe em um shell de comando:

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a>Reescrever as URLs para obter o roteamento correto

O roteamento de solicitações para componentes de página em um Blazor WebAssembly aplicativo não é tão simples quanto o roteamento de solicitações em um Blazor Server aplicativo hospedado. Considere um Blazor WebAssembly aplicativo com dois componentes:

* `Main.razor`: Carrega na raiz do aplicativo e contém um link para o `About` componente ( `href="About"` ).
* `About.razor`: `About` componente.

Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):

1. O navegador faz uma solicitação.
1. A página padrão é retornada, o que geralmente é `index.html` .
1. `index.html` Inicializa o aplicativo.
1. Blazoro roteador do é carregado e o Razor `Main` componente é renderizado.

Na página principal, selecionar o link para o `About` componente funciona no cliente, pois o Blazor roteador interrompe o navegador de fazer uma solicitação na Internet para `www.contoso.com` `About` e serve o componente renderizado em `About` si. Todas as solicitações de pontos de extremidade internos *no Blazor WebAssembly aplicativo* funcionam da mesma maneira: as solicitações não disparam solicitações baseadas em navegador para recursos hospedados no servidor na Internet. O roteador trata das solicitações internamente.

Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará. Este recurso não existe no host do aplicativo na Internet; portanto, uma resposta *404 – Não Encontrado* é retornada.

Como os navegadores fazem solicitações para hosts baseados na Internet para páginas do lado do cliente, servidores Web e serviços de hospedagem devem regravar todas as solicitações de recursos não fisicamente no servidor para a `index.html` página. Quando `index.html` é retornado, o roteador do aplicativo Blazor assume o failover e responde com o recurso correto.

Ao implantar em um servidor IIS, você pode usar o módulo de reescrita de URL com o arquivo publicado do aplicativo `web.config` . Para obter mais informações, consulte a seção [IIS](#iis) .

## <a name="hosted-deployment-with-aspnet-core"></a>Implantação hospedada com o ASP.NET Core

Uma *implantação hospedada* serve o Blazor WebAssembly aplicativo para os navegadores de um [aplicativo ASP.NET Core](xref:index) executado em um servidor Web.

O Blazor WebAssembly aplicativo cliente é publicado na `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` pasta do aplicativo de servidor, juntamente com quaisquer outros ativos da Web estáticos do aplicativo de servidor. Os dois aplicativos são implantados juntos. É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core. Para uma implantação hospedada, o Visual Studio inclui o modelo de projeto de ** Blazor WebAssembly aplicativo** ( `blazorwasm` modelo ao usar o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando) com a **`Hosted`** opção selecionada ( `-ho|--hosted` ao usar o `dotnet new` comando).

Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.

Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a>Implantação hospedada com vários Blazor WebAssembly aplicativos

### <a name="app-configuration"></a>Configuração do aplicativo

Para configurar uma solução hospedada Blazor para atender a vários Blazor WebAssembly aplicativos:

* Use uma solução hospedada existente Blazor ou crie uma nova solução a partir do Blazor modelo de projeto hospedado.

* No arquivo de projeto do aplicativo cliente, adicione uma `<StaticWebAssetBasePath>` propriedade ao `<PropertyGroup>` com um valor de `FirstApp` para definir o caminho base para os ativos estáticos do projeto:

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* Adicione um segundo aplicativo cliente à solução:

  * Adicione uma pasta chamada `SecondClient` à pasta da solução.
  * Crie um Blazor WebAssembly aplicativo chamado `SecondBlazorApp.Client` na `SecondClient` pasta do modelo de Blazor WebAssembly projeto.
  * No arquivo de projeto do aplicativo:

    * Adicione uma `<StaticWebAssetBasePath>` propriedade ao `<PropertyGroup>` com um valor de `SecondApp` :

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * Adicione uma referência de projeto ao `Shared` projeto:

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      O espaço reservado `{SOLUTION NAME}` é o nome da solução.

* No arquivo de projeto do aplicativo do servidor, crie uma referência de projeto para o aplicativo cliente adicionado:

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* No arquivo do aplicativo do servidor `Properties/launchSettings.json` , configure o `applicationUrl` do perfil Kestrel ( `{SOLUTION NAME}.Server` ) para acessar os aplicativos cliente nas portas 5001 e 5002:

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* No método do aplicativo do servidor `Startup.Configure` ( `Startup.cs` ), remova as seguintes linhas, que aparecem após a chamada para <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  Adicione middleware que mapeia solicitações para os aplicativos cliente. O exemplo a seguir configura o middleware para ser executado quando:

  * A porta de solicitação é 5001 para o aplicativo cliente original ou 5002 para o aplicativo cliente adicionado.
  * O host de solicitação é `firstapp.com` para o aplicativo cliente original ou `secondapp.com` para o aplicativo cliente adicionado.

    > [!NOTE]
    > O exemplo mostrado nesta seção requer configuração adicional para o:
    >
    > * Acessar os aplicativos em domínios de host de exemplo `firstapp.com` e `secondapp.com` .
    > * Certificados para os aplicativos cliente habilitarem a segurança TLS (HTTPS).
    >
    > A configuração necessária está além do escopo deste artigo e depende de como a solução está hospedada. Para obter mais informações, consulte os [artigos sobre host e implantar](xref:host-and-deploy/index).

  Coloque o código a seguir onde as linhas foram removidas anteriormente:

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* No controlador de previsão do tempo do aplicativo do servidor ( `Controllers/WeatherForecastController.cs` ), substitua a rota existente ( `[Route("[controller]")]` ) `WeatherForecastController` por pelas seguintes rotas:

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  O middleware adicionado ao método do aplicativo do servidor `Startup.Configure` anteriormente modifica as solicitações de entrada para `/WeatherForecast` `/FirstApp/WeatherForecast` ou `/SecondApp/WeatherForecast` dependendo da porta (5001/5002) ou do domínio ( `firstapp.com` / `secondapp.com` ). As rotas de controlador anteriores são necessárias para retornar dados meteorológicos do aplicativo de servidor para os aplicativos cliente.

### <a name="static-assets-and-class-libraries"></a>Ativos estáticos e bibliotecas de classes

Use as seguintes abordagens para ativos estáticos:

* Quando o ativo estiver na pasta do aplicativo cliente `wwwroot` , forneça seus caminhos normalmente:

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* Quando o ativo estiver na `wwwroot` pasta de uma [ Razor biblioteca de classes (RCL)](xref:blazor/components/class-libraries), referencie o ativo estático no aplicativo cliente de acordo com as diretrizes no [artigo RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

Os componentes fornecidos a um aplicativo cliente por uma biblioteca de classes são referenciados normalmente. Se algum componente exigir folhas de estilo ou arquivos JavaScript, use uma das abordagens a seguir para obter os ativos estáticos:

* O arquivo do aplicativo cliente `wwwroot/index.html` pode vincular ( `<link>` ) aos ativos estáticos.
* O componente pode usar o [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) da estrutura para obter os ativos estáticos.

As abordagens anteriores são demonstradas nos exemplos a seguir.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Os componentes fornecidos a um aplicativo cliente por uma biblioteca de classes são referenciados normalmente. Se algum componente exigir folhas de estilo ou arquivos JavaScript, o arquivo do aplicativo cliente `wwwroot/index.html` deverá incluir os links de ativo estático corretos. Essas abordagens são demonstradas nos exemplos a seguir.

::: moniker-end

Adicione o componente a seguir `Jeep` a um dos aplicativos cliente. O `Jeep` componente usa:

* Uma imagem da pasta do aplicativo cliente `wwwroot` ( `jeep-cj.png` ).
* Uma imagem de uma pasta de [ Razor biblioteca de componentes](xref:blazor/components/class-libraries) () adicionada () `JeepImage` `wwwroot` `jeep-yj.png` .
* O componente de exemplo ( `Component1` ) é criado automaticamente pelo modelo de projeto RCL quando a `JeepImage` biblioteca é adicionada à solução.

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> Não **publique imagens** de veículos publicamente, a menos que você tenha as imagens. Caso contrário, você arriscará a violação de direitos autorais.

::: moniker range=">= aspnetcore-5.0"

A imagem da biblioteca `jeep-yj.png` também pode ser adicionada ao componente da biblioteca `Component1` ( `Component1.razor` ). Para fornecer a `my-component` classe CSS à página do aplicativo cliente, vincule à folha de estilos da biblioteca usando o [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)da estrutura:

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

Uma alternativa ao uso do [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) é carregar a folha de estilos do arquivo do aplicativo cliente `wwwroot/index.html` . Essa abordagem torna a folha de estilos disponível para todos os componentes no aplicativo cliente:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

A imagem da biblioteca `jeep-yj.png` também pode ser adicionada ao componente da biblioteca `Component1` ( `Component1.razor` ):

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

O arquivo do aplicativo cliente `wwwroot/index.html` solicita a folha de estilos da biblioteca com a seguinte `<link>` marca adicionada:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

Adicione navegação ao `Jeep` componente no componente do aplicativo cliente `NavMenu` ( `Shared/NavMenu.razor` ):

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

Para obter mais informações sobre o RCLs, consulte:

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a>Implantação autônoma

Uma *implantação autônoma* serve o Blazor WebAssembly aplicativo como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes. Qualquer servidor de arquivos estático é capaz de atender ao Blazor aplicativo.

Os ativos de implantação autônomo são publicados na `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` pasta.

### <a name="azure-app-service"></a>Serviço de aplicativo do Azure

Blazor WebAssembly os aplicativos podem ser implantados em serviços Azure App no Windows, que hospedam o aplicativo no [IIS](#iis).

Blazor WebAssemblyAtualmente, não há suporte para a implantação de um aplicativo autônomo no serviço Azure app para Linux. Uma imagem do servidor Linux para hospedar o aplicativo não está disponível no momento. O trabalho está em andamento para habilitar esse cenário.

### <a name="iis"></a>IIS

O IIS é um servidor de arquivos estático com capacidade para Blazor aplicativos. Para configurar o IIS para hospedar Blazor , consulte [criar um site estático no IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Os ativos publicados são criados na `/bin/Release/{TARGET FRAMEWORK}/publish` pasta. Hospede o conteúdo da `publish` pasta no servidor Web ou no serviço de hospedagem.

#### <a name="webconfig"></a>web.config

Quando um Blazor projeto é publicado, um `web.config` arquivo é criado com a seguinte configuração do IIS:

* Os tipos MIME são definidos para as seguintes extensões de arquivo:
  * `.dll`: `application/octet-stream`
  * `.json`: `application/json`
  * `.wasm`: `application/wasm`
  * `.woff`: `application/font-woff`
  * `.woff2`: `application/font-woff`
* A compactação HTTP está habilitada para os seguintes tipos MIME:
  * `application/octet-stream`
  * `application/wasm`
* As regras do Módulo de Reescrita de URL são estabelecidas:
  * Serve o subdiretório onde residem os ativos estáticos do aplicativo ( `wwwroot/{PATH REQUESTED}` ).
  * Crie o roteamento de fallback de SPA para que as solicitações de ativos que não sejam de arquivo sejam redirecionadas para o documento padrão do aplicativo em sua pasta de ativos estáticos ( `wwwroot/index.html` ).
  
#### <a name="use-a-custom-webconfig"></a>Usar um web.config personalizado

Para usar um `web.config` arquivo personalizado, coloque o `web.config` arquivo personalizado na raiz da pasta do projeto e publique o projeto.

#### <a name="install-the-url-rewrite-module"></a>Instalação do Módulo de Regeneração de URL

O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs. Por padrão, o módulo não está instalado e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS). O módulo precisa ser baixado do site do IIS. Use o Web Platform Installer para instalar o módulo:

1. Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI. Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.
1. Copie o instalador para o servidor. Execute o instalador. Selecione o botão **Instalar** e aceite os termos de licença. Uma reinicialização do servidor não será necessária após a conclusão da instalação.

#### <a name="configure-the-website"></a>Configuração do site

Defina o **Caminho físico** do site como a pasta do aplicativo. A pasta contém:

* O `web.config` arquivo que o IIS usa para configurar o site, incluindo as regras de redirecionamento necessárias e os tipos de conteúdo do arquivo.
* A pasta de ativos estática do aplicativo.

#### <a name="host-as-an-iis-sub-app"></a>Hospedar como um subaplicativo do IIS

Se um aplicativo autônomo for hospedado como um subaplicativo do IIS, execute um dos seguintes procedimentos:

* Desabilite o manipulador do módulo ASP.NET Core herdado.

  Remova o manipulador no Blazor arquivo publicado do aplicativo `web.config` adicionando uma `<handlers>` seção ao arquivo:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Desabilite a herança da seção do aplicativo raiz (pai) `<system.webServer>` usando um `<location>` elemento com `inheritInChildApplications` definido como `false` :

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

A remoção do manipulador ou a desabilitação da herança é executada além da [configuração do caminho base do aplicativo](xref:blazor/host-and-deploy/index#app-base-path). Defina o caminho base do aplicativo no arquivo do aplicativo `index.html` como o alias do IIS usado ao configurar o subaplicativo no IIS.

#### <a name="brotli-and-gzip-compression"></a>Compactação Brotli e gzip

O IIS pode ser configurado via `web.config` para servir ativos compactados Brotli ou gzip Blazor . Para obter um exemplo de configuração, consulte [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .

#### <a name="troubleshooting"></a>Solução de problemas

Se um *500 – Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Regeneração de URL está instalado. Quando o módulo não está instalado, o `web.config` arquivo não pode ser analisado pelo IIS. Isso impede que o Gerenciador do IIS carregue a configuração do site e o site de serviços de Blazor arquivos estáticos.

Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:test/troubleshoot-azure-iis>.

### <a name="azure-storage"></a>Armazenamento do Azure

A hospedagem de arquivos estáticos de [armazenamento do Azure](/azure/storage/) permite Hospedagem de aplicativos sem servidor Blazor . Nomes de domínio personalizados, CDN (Rede de Distribuição de Conteúdo) do Azure e HTTPS são compatíveis.

Quando o serviço de blob está habilitado para hospedagem de site estático em uma conta de armazenamento:

* Defina o **Nome do documento de índice** como `index.html`.
* Defina o **Caminho do documento de erro** como `index.html`. Razor os componentes e outros pontos de extremidade que não são de arquivo não residem em caminhos físicos no conteúdo estático armazenado pelo serviço BLOB. Quando uma solicitação para um desses recursos é recebida e o Blazor roteador deve lidar, o erro *404-não encontrado* gerado pelo serviço blob roteia a solicitação para o caminho do **documento de erro**. O `index.html` blob é retornado e o Blazor roteador carrega e processa o caminho.

Se os arquivos não forem carregados no tempo de execução devido a tipos MIME inadequados nos cabeçalhos dos arquivos `Content-Type` , execute uma das seguintes ações:

* Configure suas ferramentas para definir os tipos MIME corretos ( `Content-Type` cabeçalhos) quando os arquivos são implantados.
* Altere os tipos MIME ( `Content-Type` cabeçalhos) para os arquivos depois que o aplicativo for implantado.

  Em Gerenciador de Armazenamento (portal do Azure) para cada arquivo:
  
  1. Clique com o botão direito do mouse no arquivo e selecione **Propriedades**.
  1. Defina o **ContentType** e selecione o botão **salvar** .

Para mais informações, confira [Hospedagem de site estático no Armazenamento do Azure](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

O arquivo a seguir `nginx.conf` é simplificado para mostrar como configurar o Nginx para enviar o `index.html` arquivo sempre que ele não encontrar um arquivo correspondente no disco.

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Ao definir o [limite de taxa de intermitência Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) com [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) , os Blazor WebAssembly aplicativos podem exigir um grande `burst` valor de parâmetro para acomodar o número relativamente grande de solicitações feitas por um aplicativo. Inicialmente, defina o valor para pelo menos 60:

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

Aumente o valor se as ferramentas de desenvolvedor do navegador ou uma ferramenta de tráfego de rede indicar que as solicitações estão recebendo um código de status de *503-Serviço indisponível* .

Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).

### <a name="nginx-in-docker"></a>Nginx no Docker

Para hospedar Blazor no Docker usando o Nginx, configure o Dockerfile para usar a imagem Nginx baseada em Alpine. Atualize o Dockerfile para copiar o `nginx.config` arquivo para o contêiner.

Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Para implantar um Blazor WebAssembly aplicativo no CentOS 7 ou posterior:

1. Crie o arquivo de configuração do Apache. O exemplo a seguir é um arquivo de configuração simplificado ( `blazorapp.config` ):

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Coloque o arquivo de configuração do Apache no `/etc/httpd/conf.d/` diretório, que é o diretório de configuração padrão do Apache no CentOS 7.

1. Coloque os arquivos do aplicativo no `/var/www/blazorapp` diretório (o local especificado para `DocumentRoot` o no arquivo de configuração).

1. Reinicie o serviço apache.

Para obter mais informações, confira [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>GitHub Pages

Para tratar as regravações de URL, adicione um `wwwroot/404.html` arquivo com um script que manipula o redirecionamento da solicitação para a `index.html` página. Para obter um exemplo, consulte o [ Blazor repositório GitHub do SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* [Site ativo](https://stevesandersonms.github.io/BlazorOnGitHubPages/))

Ao usar um site do projeto em vez de um site da organização, atualize a `<base>` marca no `wwwroot/index.html` . Defina o `href` valor do atributo para o nome do repositório GitHub com uma barra à direita (por exemplo, `/my-repository/` ). No [ Blazor repositório GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), a base `href` é atualizada em publicar pelo arquivo de [ `.github/workflows/main.yml` configuração](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).

> [!NOTE]
> O [ Blazor repositório GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) não é proprietário, mantido ou tem suporte do .net Foundation ou da Microsoft.

## <a name="host-configuration-values"></a>Valores de configuração do host

os [ Blazor WebAssembly aplicativos](xref:blazor/hosting-models#blazor-webassembly) podem aceitar os seguintes valores de configuração de host como argumentos de linha de comando em tempo de execução no ambiente de desenvolvimento.

### <a name="content-root"></a>Raiz do conteúdo

O `--contentroot` argumento define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo ([raiz de conteúdo](xref:fundamentals/index#content-root)). Nos exemplos a seguir, `/content-root-path` é o caminho raiz do conteúdo do aplicativo.

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** . Esta configuração é usada quando o aplicativo é executado com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**. Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Caminho base

O `--pathbase` argumento define o caminho base do aplicativo para um aplicativo executado localmente com um caminho de URL relativo não raiz (a `<base>` marca `href` é definida para um caminho diferente `/` de para preparo e produção). Nos exemplos a seguir, `/relative-URL-path` é o caminho base do aplicativo. Para obter mais informações, consulte [caminho base do aplicativo](xref:blazor/host-and-deploy/index#app-base-path).

> [!IMPORTANT]
> Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`. Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/">` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** . Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**. Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URLs

O argumento `--urls` define os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.

* Passe o argumento ao executar o aplicativo localmente em um prompt de comando. No diretório do aplicativo, execute:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** . Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**. Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a>Configurar o corte

Blazor executa a corte de IL (linguagem intermediária) em cada Build de versão para remover o IL desnecessário dos assemblies de saída. Para obter mais informações, consulte <xref:blazor/host-and-deploy/configure-trimmer>.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a>Configurar o vinculador

Blazor executa a vinculação de IL (linguagem intermediária) em cada Build de versão para remover o IL desnecessário dos assemblies de saída. Para obter mais informações, consulte <xref:blazor/host-and-deploy/configure-linker>.

::: moniker-end

## <a name="custom-boot-resource-loading"></a>Carregamento de recurso de inicialização personalizada

Um Blazor WebAssembly aplicativo pode ser inicializado com a `loadBootResource` função para substituir o mecanismo de carregamento do recurso de inicialização interno. Use `loadBootResource` para os seguintes cenários:

* Permitir que os usuários carreguem recursos estáticos, como dados de fuso horário ou `dotnet.wasm` de uma CDN.
* Carregue assemblies compactados usando uma solicitação HTTP e descompacte-os no cliente para hosts que não dão suporte à busca de conteúdo compactado do servidor.
* Recursos de alias para um nome diferente redirecionando cada `fetch` solicitação para um novo nome.

`loadBootResource` os parâmetros aparecem na tabela a seguir.

| Parâmetro    | Descrição |
| ------------ | ----------- |
| `type`       | Tipo do recurso. Tipos permissiváveis: `assembly` ,, `pdb` , `dotnetjs` `dotnetwasm` , `timezonedata` |
| `name`       | O nome do recurso. |
| `defaultUri` | O URI relativo ou absoluto do recurso. |
| `integrity`  | A cadeia de caracteres de integridade que representa o conteúdo esperado na resposta. |

`loadBootResource` retorna qualquer um dos seguintes para substituir o processo de carregamento:

* Cadeia de caracteres de URI. No exemplo a seguir ( `wwwroot/index.html` ), os seguintes arquivos são atendidos de uma CDN em `https://my-awesome-cdn.com/` :

  * `dotnet.*.js`
  * `dotnet.wasm`
  * Dados de fuso horário

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* `Promise<Response>`. Passe o `integrity` parâmetro em um cabeçalho para manter o comportamento de verificação de integridade padrão.

  O exemplo a seguir ( `wwwroot/index.html` ) adiciona um cabeçalho HTTP personalizado às solicitações de saída e passa o `integrity` parâmetro para a `fetch` chamada:
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* `null`/`undefined`, o que resulta no comportamento de carregamento padrão.

As fontes externas devem retornar os cabeçalhos CORS necessários para que os navegadores permitam o carregamento de recursos entre origens. O CDNs geralmente fornece os cabeçalhos necessários por padrão.

Você só precisa especificar tipos para comportamentos personalizados. Tipos não especificados para `loadBootResource` são carregados pela estrutura de acordo com seus comportamentos de carregamento padrão.

## <a name="change-the-filename-extension-of-dll-files"></a>Alterar a extensão do nome de arquivo de arquivos DLL

Caso você tenha a necessidade de alterar as extensões de nome de arquivo dos arquivos publicados do aplicativo `.dll` , siga as orientações nesta seção.

Depois de publicar o aplicativo, use um script de shell ou o pipeline de compilação DevOps para renomear `.dll` arquivos para usar uma extensão de arquivo diferente. Direcione os `.dll` arquivos no `wwwroot` diretório da saída publicada do aplicativo (por exemplo, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).

Nos exemplos a seguir, `.dll` os arquivos são renomeados para usar a `.bin` extensão de arquivo.

No Windows:

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

No Linux ou macOS:

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
Para usar uma extensão de arquivo diferente da `.bin` , substitua `.bin` os comandos anteriores.

Para resolver os arquivos compactados `blazor.boot.json.gz` `blazor.boot.json.br` , adote uma das seguintes abordagens:

* Remova os arquivos compactados `blazor.boot.json.gz` `blazor.boot.json.br` . A compactação está desabilitada com essa abordagem.
* Recompacte o `blazor.boot.json` arquivo atualizado.

As diretrizes anteriores também se aplicam quando os ativos de trabalho do serviço estão em uso. Remova ou recompacte `wwwroot/service-worker-assets.js.br` e `wwwroot/service-worker-assets.js.gz` . Caso contrário, as verificações de integridade de arquivo falharão no navegador.

O exemplo de Windows a seguir usa um script do PowerShell colocado na raiz do projeto.

`ChangeDLLExtensions.ps1:`:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

No arquivo de projeto, o script é executado após a publicação do aplicativo:

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> Ao renomear e carregar lentos os mesmos assemblies, consulte as diretrizes em <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .

Para fornecer comentários, visite [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).
