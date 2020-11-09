---
title: 'Hospedar e implantar ASP.NET Core Blazor WebAssembly'
author: guardrex
description: 'Saiba como hospedar e implantar um Blazor aplicativo usando ASP.NET Core, CDN (redes de distribuição de conteúdo), servidores de arquivos e páginas do github.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 7ae462ff9abd06fe4ab4b3e00a71515b76b0ee7d
ms.sourcegitcommit: bb475e69cb647f22cf6d2c6f93d0836c160080d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94339978"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="c6ca7-103">Hospedar e implantar ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c6ca7-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="c6ca7-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)e [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="c6ca7-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="c6ca7-105">Com o [ Blazor WebAssembly modelo de hospedagem](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="c6ca7-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="c6ca7-106">O Blazor aplicativo, suas dependências e o tempo de execução do .NET são baixados para o navegador em paralelo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="c6ca7-107">O aplicativo é executado diretamente no thread da interface do usuário do navegador.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="c6ca7-108">Há suporte para as seguintes estratégias de implantação:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="c6ca7-109">O Blazor aplicativo é servido por um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="c6ca7-110">Esta estratégia é abordada na seção [Implantação hospedada com o ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="c6ca7-111">O Blazor aplicativo é colocado em um servidor Web ou serviço de hospedagem estática, em que o .net não é usado para servir o Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="c6ca7-112">Essa estratégia é abordada na seção [implantação autônoma](#standalone-deployment) , que inclui informações sobre como hospedar um Blazor WebAssembly aplicativo como um subaplicativo do IIS.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="c6ca7-113">Compactação</span><span class="sxs-lookup"><span data-stu-id="c6ca7-113">Compression</span></span>

<span data-ttu-id="c6ca7-114">Quando um Blazor WebAssembly aplicativo é publicado, a saída é compactada estaticamente durante a publicação para reduzir o tamanho do aplicativo e remover a sobrecarga de compactação de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="c6ca7-115">Os seguintes algoritmos de compactação são usados:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="c6ca7-116">[Brotli](https://tools.ietf.org/html/rfc7932) (nível mais alto)</span><span class="sxs-lookup"><span data-stu-id="c6ca7-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="c6ca7-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="c6ca7-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="c6ca7-118">Blazor o se baseia no host para o fornecer os arquivos compactados apropriados.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="c6ca7-119">Ao usar um projeto ASP.NET Core hospedado, o projeto host é capaz de executar a negociação de conteúdo e fornecer os arquivos compactados estaticamente.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="c6ca7-120">Ao hospedar um Blazor WebAssembly aplicativo autônomo, um trabalho adicional pode ser necessário para garantir que arquivos compactados estaticamente sejam atendidos:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="c6ca7-121">Para `web.config` a configuração de compactação do IIS, consulte a seção [IIS: Brotli e a compactação Gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="c6ca7-122">Ao hospedar soluções de hospedagem estática que não dão suporte à negociação de conteúdo de arquivo compactado estaticamente, como páginas do GitHub, considere configurar o aplicativo para buscar e decodificar arquivos compactados Brotli:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="c6ca7-123">Obtenha o decodificador Brotli do JavaScript do [repositório GitHub do Google/Brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="c6ca7-124">A partir de setembro de 2020, o arquivo de decodificador é nomeado `decode.js` e encontrado na [ `js` pasta](https://github.com/google/brotli/tree/master/js)do repositório.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="c6ca7-125">Uma regressão está presente na versão reduzidos do `decode.js` script ( `decode.min.js` ) no [repositório GitHub do Google/brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="c6ca7-126">Reduzir o script por conta própria ou use o [pacote NPM](https://www.npmjs.com/package/brotli) até que a janela de problema [. BrotliDecode não esteja definida em decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) seja resolvida.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="c6ca7-127">O código de exemplo nesta seção usa a versão **unminified** do script.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="c6ca7-128">Atualize o aplicativo para usar o decodificador.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-128">Update the app to use the decoder.</span></span> <span data-ttu-id="c6ca7-129">Altere a marcação dentro da `<body>` marca de fechamento `wwwroot/index.html` para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
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
 
<span data-ttu-id="c6ca7-130">Para desabilitar a compactação, adicione a `BlazorEnableCompression` Propriedade MSBuild ao arquivo de projeto do aplicativo e defina o valor como `false` :</span><span class="sxs-lookup"><span data-stu-id="c6ca7-130">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="c6ca7-131">A `BlazorEnableCompression` propriedade pode ser passada para o [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando com a seguinte sintaxe em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-131">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="c6ca7-132">Reescrever as URLs para obter o roteamento correto</span><span class="sxs-lookup"><span data-stu-id="c6ca7-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="c6ca7-133">O roteamento de solicitações para componentes de página em um Blazor WebAssembly aplicativo não é tão simples quanto o roteamento de solicitações em um Blazor Server aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-133">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="c6ca7-134">Considere um Blazor WebAssembly aplicativo com dois componentes:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-134">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="c6ca7-135">`Main.razor`: Carrega na raiz do aplicativo e contém um link para o `About` componente ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="c6ca7-136">`About.razor`: `About` componente.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="c6ca7-137">Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="c6ca7-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="c6ca7-138">O navegador faz uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-138">The browser makes a request.</span></span>
1. <span data-ttu-id="c6ca7-139">A página padrão é retornada, o que geralmente é `index.html` .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="c6ca7-140">`index.html` Inicializa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="c6ca7-141">Blazoro roteador do é carregado e o Razor `Main` componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-141">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="c6ca7-142">Na página principal, selecionar o link para o `About` componente funciona no cliente, pois o Blazor roteador interrompe o navegador de fazer uma solicitação na Internet para `www.contoso.com` `About` e serve o componente renderizado em `About` si.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-142">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="c6ca7-143">Todas as solicitações de pontos de extremidade internos *no Blazor WebAssembly aplicativo* funcionam da mesma maneira: as solicitações não disparam solicitações baseadas em navegador para recursos hospedados no servidor na Internet.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-143">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="c6ca7-144">O roteador trata das solicitações internamente.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-144">The router handles the requests internally.</span></span>

<span data-ttu-id="c6ca7-145">Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="c6ca7-146">Este recurso não existe no host do aplicativo na Internet; portanto, uma resposta *404 – Não Encontrado* é retornada.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="c6ca7-147">Como os navegadores fazem solicitações para hosts baseados na Internet para páginas do lado do cliente, servidores Web e serviços de hospedagem devem regravar todas as solicitações de recursos não fisicamente no servidor para a `index.html` página.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="c6ca7-148">Quando `index.html` é retornado, o roteador do aplicativo Blazor assume o failover e responde com o recurso correto.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-148">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="c6ca7-149">Ao implantar em um servidor IIS, você pode usar o módulo de reescrita de URL com o arquivo publicado do aplicativo `web.config` .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="c6ca7-150">Para obter mais informações, consulte a seção [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="c6ca7-151">Implantação hospedada com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6ca7-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="c6ca7-152">Uma *implantação hospedada* serve o Blazor WebAssembly aplicativo para os navegadores de um [aplicativo ASP.NET Core](xref:index) executado em um servidor Web.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-152">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="c6ca7-153">O Blazor WebAssembly aplicativo cliente é publicado na `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` pasta do aplicativo de servidor, juntamente com quaisquer outros ativos da Web estáticos do aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-153">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="c6ca7-154">Os dois aplicativos são implantados juntos.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-154">The two apps are deployed together.</span></span> <span data-ttu-id="c6ca7-155">É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="c6ca7-156">Para uma implantação hospedada, o Visual Studio inclui o modelo de projeto de **Blazor WebAssembly aplicativo** ( `blazorwasm` modelo ao usar o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando) com a **`Hosted`** opção selecionada ( `-ho|--hosted` ao usar o `dotnet new` comando).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-156">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="c6ca7-157">Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="c6ca7-158">Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="c6ca7-159">Implantação hospedada com vários Blazor WebAssembly aplicativos</span><span class="sxs-lookup"><span data-stu-id="c6ca7-159">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="c6ca7-160">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="c6ca7-160">App configuration</span></span>

<span data-ttu-id="c6ca7-161">Para configurar uma solução hospedada Blazor para atender a vários Blazor WebAssembly aplicativos:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-161">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="c6ca7-162">Use uma solução hospedada existente Blazor ou crie uma nova solução a partir do Blazor modelo de projeto hospedado.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-162">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="c6ca7-163">No arquivo de projeto do aplicativo cliente, adicione uma `<StaticWebAssetBasePath>` propriedade ao `<PropertyGroup>` com um valor de `FirstApp` para definir o caminho base para os ativos estáticos do projeto:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-163">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="c6ca7-164">Adicione um segundo aplicativo cliente à solução:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-164">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="c6ca7-165">Adicione uma pasta chamada `SecondClient` à pasta da solução.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-165">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="c6ca7-166">Crie um Blazor WebAssembly aplicativo chamado `SecondBlazorApp.Client` na `SecondClient` pasta do modelo de Blazor WebAssembly projeto.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-166">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="c6ca7-167">No arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-167">In the app's project file:</span></span>

    * <span data-ttu-id="c6ca7-168">Adicione uma `<StaticWebAssetBasePath>` propriedade ao `<PropertyGroup>` com um valor de `SecondApp` :</span><span class="sxs-lookup"><span data-stu-id="c6ca7-168">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="c6ca7-169">Adicione uma referência de projeto ao `Shared` projeto:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-169">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="c6ca7-170">O espaço reservado `{SOLUTION NAME}` é o nome da solução.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-170">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="c6ca7-171">No arquivo de projeto do aplicativo do servidor, crie uma referência de projeto para o aplicativo cliente adicionado:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-171">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="c6ca7-172">No arquivo do aplicativo do servidor `Properties/launchSettings.json` , configure o `applicationUrl` do perfil Kestrel ( `{SOLUTION NAME}.Server` ) para acessar os aplicativos cliente nas portas 5001 e 5002:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-172">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="c6ca7-173">No método do aplicativo do servidor `Startup.Configure` ( `Startup.cs` ), remova as seguintes linhas, que aparecem após a chamada para <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :</span><span class="sxs-lookup"><span data-stu-id="c6ca7-173">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

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

  <span data-ttu-id="c6ca7-174">Adicione middleware que mapeia solicitações para os aplicativos cliente.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-174">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="c6ca7-175">O exemplo a seguir configura o middleware para ser executado quando:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-175">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="c6ca7-176">A porta de solicitação é 5001 para o aplicativo cliente original ou 5002 para o aplicativo cliente adicionado.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-176">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="c6ca7-177">O host de solicitação é `firstapp.com` para o aplicativo cliente original ou `secondapp.com` para o aplicativo cliente adicionado.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-177">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="c6ca7-178">O exemplo mostrado nesta seção requer configuração adicional para o:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-178">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="c6ca7-179">Acessar os aplicativos em domínios de host de exemplo `firstapp.com` e `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-179">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="c6ca7-180">Certificados para os aplicativos cliente habilitarem a segurança TLS (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-180">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="c6ca7-181">A configuração necessária está além do escopo deste artigo e depende de como a solução está hospedada.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-181">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="c6ca7-182">Para obter mais informações, consulte os [artigos sobre host e implantar](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-182">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="c6ca7-183">Coloque o código a seguir onde as linhas foram removidas anteriormente:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-183">Place the following code where the lines were removed earlier:</span></span>

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

* <span data-ttu-id="c6ca7-184">No controlador de previsão do tempo do aplicativo do servidor ( `Controllers/WeatherForecastController.cs` ), substitua a rota existente ( `[Route("[controller]")]` ) `WeatherForecastController` por pelas seguintes rotas:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-184">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="c6ca7-185">O middleware adicionado ao método do aplicativo do servidor `Startup.Configure` anteriormente modifica as solicitações de entrada para `/WeatherForecast` `/FirstApp/WeatherForecast` ou `/SecondApp/WeatherForecast` dependendo da porta (5001/5002) ou do domínio ( `firstapp.com` / `secondapp.com` ).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-185">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="c6ca7-186">As rotas de controlador anteriores são necessárias para retornar dados meteorológicos do aplicativo de servidor para os aplicativos cliente.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-186">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="c6ca7-187">Ativos estáticos e bibliotecas de classes</span><span class="sxs-lookup"><span data-stu-id="c6ca7-187">Static assets and class libraries</span></span>

<span data-ttu-id="c6ca7-188">Use as seguintes abordagens para ativos estáticos:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-188">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="c6ca7-189">Quando o ativo estiver na pasta do aplicativo cliente `wwwroot` , forneça seus caminhos normalmente:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-189">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="c6ca7-190">Quando o ativo estiver na `wwwroot` pasta de uma [ Razor biblioteca de classes (RCL)](xref:blazor/components/class-libraries), referencie o ativo estático no aplicativo cliente de acordo com as diretrizes no [artigo RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="c6ca7-190">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

Components provided to a client app by a class library are referenced normally. If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:

* The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.
* The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.

The preceding approaches are demonstrated in the following examples.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="c6ca7-191">Os componentes fornecidos a um aplicativo cliente por uma biblioteca de classes são referenciados normalmente.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-191">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="c6ca7-192">Se algum componente exigir folhas de estilo ou arquivos JavaScript, o arquivo do aplicativo cliente `wwwroot/index.html` deverá incluir os links de ativo estático corretos.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-192">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="c6ca7-193">Essas abordagens são demonstradas nos exemplos a seguir.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-193">These approaches are demonstrated in the following examples.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="c6ca7-194">Adicione o componente a seguir `Jeep` a um dos aplicativos cliente.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-194">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="c6ca7-195">O `Jeep` componente usa:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-195">The `Jeep` component uses:</span></span>

* <span data-ttu-id="c6ca7-196">Uma imagem da pasta do aplicativo cliente `wwwroot` ( `jeep-cj.png` ).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-196">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="c6ca7-197">Uma imagem de uma pasta de [ Razor biblioteca de componentes](xref:blazor/components/class-libraries) () adicionada () `JeepImage` `wwwroot` `jeep-yj.png` .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-197">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="c6ca7-198">O componente de exemplo ( `Component1` ) é criado automaticamente pelo modelo de projeto RCL quando a `JeepImage` biblioteca é adicionada à solução.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-198">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

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
> <span data-ttu-id="c6ca7-199">Não **publique imagens** de veículos publicamente, a menos que você tenha as imagens.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-199">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="c6ca7-200">Caso contrário, você arriscará a violação de direitos autorais.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-200">Otherwise, you risk copyright infringement.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):

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

An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file. This approach makes the stylesheet available to all of the components in the client app:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="c6ca7-201">A imagem da biblioteca `jeep-yj.png` também pode ser adicionada ao componente da biblioteca `Component1` ( `Component1.razor` ):</span><span class="sxs-lookup"><span data-stu-id="c6ca7-201">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

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

<span data-ttu-id="c6ca7-202">O arquivo do aplicativo cliente `wwwroot/index.html` solicita a folha de estilos da biblioteca com a seguinte `<link>` marca adicionada:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-202">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="c6ca7-203">Adicione navegação ao `Jeep` componente no componente do aplicativo cliente `NavMenu` ( `Shared/NavMenu.razor` ):</span><span class="sxs-lookup"><span data-stu-id="c6ca7-203">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="c6ca7-204">Para obter mais informações sobre o RCLs, consulte:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-204">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="c6ca7-205">Implantação autônoma</span><span class="sxs-lookup"><span data-stu-id="c6ca7-205">Standalone deployment</span></span>

<span data-ttu-id="c6ca7-206">Uma *implantação autônoma* serve o Blazor WebAssembly aplicativo como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-206">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="c6ca7-207">Qualquer servidor de arquivos estático é capaz de atender ao Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-207">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="c6ca7-208">Os ativos de implantação autônomo são publicados na `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` pasta.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-208">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="c6ca7-209">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="c6ca7-209">Azure App Service</span></span>

<span data-ttu-id="c6ca7-210">Blazor WebAssembly os aplicativos podem ser implantados em serviços Azure App no Windows, que hospedam o aplicativo no [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-210">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="c6ca7-211">Blazor WebAssemblyAtualmente, não há suporte para a implantação de um aplicativo autônomo no serviço Azure app para Linux.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-211">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="c6ca7-212">Uma imagem do servidor Linux para hospedar o aplicativo não está disponível no momento.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-212">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="c6ca7-213">O trabalho está em andamento para habilitar esse cenário.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-213">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="c6ca7-214">IIS</span><span class="sxs-lookup"><span data-stu-id="c6ca7-214">IIS</span></span>

<span data-ttu-id="c6ca7-215">O IIS é um servidor de arquivos estático com capacidade para Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-215">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="c6ca7-216">Para configurar o IIS para hospedar Blazor , consulte [criar um site estático no IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-216">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="c6ca7-217">Os ativos publicados são criados na `/bin/Release/{TARGET FRAMEWORK}/publish` pasta.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-217">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="c6ca7-218">Hospede o conteúdo da `publish` pasta no servidor Web ou no serviço de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-218">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="c6ca7-219">web.config</span><span class="sxs-lookup"><span data-stu-id="c6ca7-219">web.config</span></span>

<span data-ttu-id="c6ca7-220">Quando um Blazor projeto é publicado, um `web.config` arquivo é criado com a seguinte configuração do IIS:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-220">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="c6ca7-221">Os tipos MIME são definidos para as seguintes extensões de arquivo:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-221">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="c6ca7-222">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="c6ca7-222">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="c6ca7-223">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="c6ca7-223">`.json`: `application/json`</span></span>
  * <span data-ttu-id="c6ca7-224">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="c6ca7-224">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="c6ca7-225">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="c6ca7-225">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="c6ca7-226">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="c6ca7-226">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="c6ca7-227">A compactação HTTP está habilitada para os seguintes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-227">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="c6ca7-228">As regras do Módulo de Reescrita de URL são estabelecidas:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-228">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="c6ca7-229">Serve o subdiretório onde residem os ativos estáticos do aplicativo ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-229">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="c6ca7-230">Crie o roteamento de fallback de SPA para que as solicitações de ativos que não sejam de arquivo sejam redirecionadas para o documento padrão do aplicativo em sua pasta de ativos estáticos ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-230">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="c6ca7-231">Usar um web.config personalizado</span><span class="sxs-lookup"><span data-stu-id="c6ca7-231">Use a custom web.config</span></span>

<span data-ttu-id="c6ca7-232">Para usar um `web.config` arquivo personalizado, coloque o `web.config` arquivo personalizado na raiz da pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-232">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder.</span></span> <span data-ttu-id="c6ca7-233">Configure o projeto para publicar ativos específicos do IIS usando `PublishIISAssets` no arquivo de projeto do aplicativo e publique o projeto:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-233">Configure the project to publish IIS-specific assets using `PublishIISAssets` in the app's project file and publish the project:</span></span>

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="c6ca7-234">Instalação do Módulo de Regeneração de URL</span><span class="sxs-lookup"><span data-stu-id="c6ca7-234">Install the URL Rewrite Module</span></span>

<span data-ttu-id="c6ca7-235">O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-235">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="c6ca7-236">Por padrão, o módulo não está instalado e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-236">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="c6ca7-237">O módulo precisa ser baixado do site do IIS.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-237">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="c6ca7-238">Use o Web Platform Installer para instalar o módulo:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-238">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="c6ca7-239">Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-239">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="c6ca7-240">Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-240">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="c6ca7-241">Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-241">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="c6ca7-242">Copie o instalador para o servidor.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-242">Copy the installer to the server.</span></span> <span data-ttu-id="c6ca7-243">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-243">Run the installer.</span></span> <span data-ttu-id="c6ca7-244">Selecione o botão **Instalar** e aceite os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-244">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="c6ca7-245">Uma reinicialização do servidor não será necessária após a conclusão da instalação.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-245">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="c6ca7-246">Configuração do site</span><span class="sxs-lookup"><span data-stu-id="c6ca7-246">Configure the website</span></span>

<span data-ttu-id="c6ca7-247">Defina o **Caminho físico** do site como a pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-247">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="c6ca7-248">A pasta contém:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-248">The folder contains:</span></span>

* <span data-ttu-id="c6ca7-249">O `web.config` arquivo que o IIS usa para configurar o site, incluindo as regras de redirecionamento necessárias e os tipos de conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-249">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="c6ca7-250">A pasta de ativos estática do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-250">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="c6ca7-251">Hospedar como um subaplicativo do IIS</span><span class="sxs-lookup"><span data-stu-id="c6ca7-251">Host as an IIS sub-app</span></span>

<span data-ttu-id="c6ca7-252">Se um aplicativo autônomo for hospedado como um subaplicativo do IIS, execute um dos seguintes procedimentos:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-252">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="c6ca7-253">Desabilite o manipulador do módulo ASP.NET Core herdado.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-253">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="c6ca7-254">Remova o manipulador no Blazor arquivo publicado do aplicativo `web.config` adicionando uma `<handlers>` seção ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-254">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="c6ca7-255">Desabilite a herança da seção do aplicativo raiz (pai) `<system.webServer>` usando um `<location>` elemento com `inheritInChildApplications` definido como `false` :</span><span class="sxs-lookup"><span data-stu-id="c6ca7-255">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="c6ca7-256">A remoção do manipulador ou a desabilitação da herança é executada além da [configuração do caminho base do aplicativo](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-256">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="c6ca7-257">Defina o caminho base do aplicativo no arquivo do aplicativo `index.html` como o alias do IIS usado ao configurar o subaplicativo no IIS.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-257">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="c6ca7-258">Compactação Brotli e gzip</span><span class="sxs-lookup"><span data-stu-id="c6ca7-258">Brotli and Gzip compression</span></span>

<span data-ttu-id="c6ca7-259">*Esta seção se aplica somente a Blazor WebAssembly aplicativos autônomos. Blazor os aplicativos hospedados usam um arquivo de aplicativo ASP.NET Core padrão `web.config` , não o arquivo vinculado nesta seção.*</span><span class="sxs-lookup"><span data-stu-id="c6ca7-259">*This section only applies to standalone Blazor WebAssembly apps. Hosted Blazor apps use a default ASP.NET Core app `web.config` file, not the file linked in this section.*</span></span>

<span data-ttu-id="c6ca7-260">O IIS pode ser configurado via `web.config` para servir ativos compactados Brotli ou gzip Blazor para aplicativos autônomos Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-260">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets for standalone Blazor WebAssembly apps.</span></span> <span data-ttu-id="c6ca7-261">Para obter um exemplo de arquivo de configuração, consulte [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-261">For an example configuration file, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

<span data-ttu-id="c6ca7-262">A configuração adicional do arquivo de exemplo `web.config` pode ser necessária nos seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-262">Additional configuration of the example `web.config` file might be required in the following scenarios:</span></span>

* <span data-ttu-id="c6ca7-263">A especificação do aplicativo chama um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-263">The app's specification calls for either of the following:</span></span>
  * <span data-ttu-id="c6ca7-264">Servindo arquivos compactados que não estão configurados pelo arquivo de exemplo `web.config` .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-264">Serving compressed files that aren't configured by the example `web.config` file.</span></span>
  * <span data-ttu-id="c6ca7-265">Servindo arquivos compactados configurados pelo arquivo de exemplo `web.config` em um formato descompactado.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-265">Serving compressed files configured by the example `web.config` file in an uncompressed format.</span></span>
* <span data-ttu-id="c6ca7-266">A configuração do IIS do servidor (por exemplo, `applicationHost.config` ) fornece padrões do IIS no nível do servidor.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-266">The server's IIS configuration (for example, `applicationHost.config`) provides server-level IIS defaults.</span></span> <span data-ttu-id="c6ca7-267">Dependendo da configuração no nível do servidor, o aplicativo pode exigir uma configuração diferente do IIS do que o arquivo de exemplo `web.config` contém.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-267">Depending on the server-level configuration, the app might require a different IIS configuration than what the example `web.config` file contains.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="c6ca7-268">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="c6ca7-268">Troubleshooting</span></span>

<span data-ttu-id="c6ca7-269">Se um *500 – Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Regeneração de URL está instalado.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-269">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="c6ca7-270">Quando o módulo não está instalado, o `web.config` arquivo não pode ser analisado pelo IIS.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-270">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="c6ca7-271">Isso impede que o Gerenciador do IIS carregue a configuração do site e o site de serviços de Blazor arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-271">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="c6ca7-272">Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-272">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="c6ca7-273">Armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="c6ca7-273">Azure Storage</span></span>

<span data-ttu-id="c6ca7-274">A hospedagem de arquivos estáticos de [armazenamento do Azure](/azure/storage/) permite Hospedagem de aplicativos sem servidor Blazor .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-274">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="c6ca7-275">Nomes de domínio personalizados, CDN (Rede de Distribuição de Conteúdo) do Azure e HTTPS são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-275">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="c6ca7-276">Quando o serviço de blob está habilitado para hospedagem de site estático em uma conta de armazenamento:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-276">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="c6ca7-277">Defina o **Nome do documento de índice** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-277">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="c6ca7-278">Defina o **Caminho do documento de erro** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-278">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="c6ca7-279">Razor os componentes e outros pontos de extremidade que não são de arquivo não residem em caminhos físicos no conteúdo estático armazenado pelo serviço BLOB.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-279">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="c6ca7-280">Quando uma solicitação para um desses recursos é recebida e o Blazor roteador deve lidar, o erro *404-não encontrado* gerado pelo serviço blob roteia a solicitação para o caminho do **documento de erro**.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-280">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="c6ca7-281">O `index.html` blob é retornado e o Blazor roteador carrega e processa o caminho.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-281">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="c6ca7-282">Se os arquivos não forem carregados no tempo de execução devido a tipos MIME inadequados nos cabeçalhos dos arquivos `Content-Type` , execute uma das seguintes ações:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-282">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="c6ca7-283">Configure suas ferramentas para definir os tipos MIME corretos ( `Content-Type` cabeçalhos) quando os arquivos são implantados.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-283">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="c6ca7-284">Altere os tipos MIME ( `Content-Type` cabeçalhos) para os arquivos depois que o aplicativo for implantado.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-284">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="c6ca7-285">Em Gerenciador de Armazenamento (portal do Azure) para cada arquivo:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-285">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="c6ca7-286">Clique com o botão direito do mouse no arquivo e selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-286">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="c6ca7-287">Defina o **ContentType** e selecione o botão **salvar** .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-287">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="c6ca7-288">Para mais informações, confira [Hospedagem de site estático no Armazenamento do Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-288">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="c6ca7-289">Nginx</span><span class="sxs-lookup"><span data-stu-id="c6ca7-289">Nginx</span></span>

<span data-ttu-id="c6ca7-290">O arquivo a seguir `nginx.conf` é simplificado para mostrar como configurar o Nginx para enviar o `index.html` arquivo sempre que ele não encontrar um arquivo correspondente no disco.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-290">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="c6ca7-291">Ao definir o [limite de taxa de intermitência Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) com [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) , os Blazor WebAssembly aplicativos podem exigir um grande `burst` valor de parâmetro para acomodar o número relativamente grande de solicitações feitas por um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-291">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="c6ca7-292">Inicialmente, defina o valor para pelo menos 60:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-292">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="c6ca7-293">Aumente o valor se as ferramentas de desenvolvedor do navegador ou uma ferramenta de tráfego de rede indicar que as solicitações estão recebendo um código de status de *503-Serviço indisponível* .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-293">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="c6ca7-294">Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-294">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="c6ca7-295">Nginx no Docker</span><span class="sxs-lookup"><span data-stu-id="c6ca7-295">Nginx in Docker</span></span>

<span data-ttu-id="c6ca7-296">Para hospedar Blazor no Docker usando o Nginx, configure o Dockerfile para usar a imagem Nginx baseada em Alpine.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-296">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="c6ca7-297">Atualize o Dockerfile para copiar o `nginx.config` arquivo para o contêiner.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-297">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="c6ca7-298">Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-298">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="c6ca7-299">Apache</span><span class="sxs-lookup"><span data-stu-id="c6ca7-299">Apache</span></span>

<span data-ttu-id="c6ca7-300">Para implantar um Blazor WebAssembly aplicativo no CentOS 7 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-300">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="c6ca7-301">Crie o arquivo de configuração do Apache.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-301">Create the Apache configuration file.</span></span> <span data-ttu-id="c6ca7-302">O exemplo a seguir é um arquivo de configuração simplificado ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="c6ca7-302">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="c6ca7-303">Coloque o arquivo de configuração do Apache no `/etc/httpd/conf.d/` diretório, que é o diretório de configuração padrão do Apache no CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-303">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="c6ca7-304">Coloque os arquivos do aplicativo no `/var/www/blazorapp` diretório (o local especificado para `DocumentRoot` o no arquivo de configuração).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-304">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="c6ca7-305">Reinicie o serviço apache.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-305">Restart the Apache service.</span></span>

<span data-ttu-id="c6ca7-306">Para obter mais informações, confira [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-306">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="c6ca7-307">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="c6ca7-307">GitHub Pages</span></span>

<span data-ttu-id="c6ca7-308">Para tratar as regravações de URL, adicione um `wwwroot/404.html` arquivo com um script que manipula o redirecionamento da solicitação para a `index.html` página.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-308">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="c6ca7-309">Para obter um exemplo, consulte o [ Blazor repositório GitHub do SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="c6ca7-309">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="c6ca7-310">[Site ativo](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="c6ca7-310">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="c6ca7-311">Ao usar um site do projeto em vez de um site da organização, atualize a `<base>` marca no `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-311">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="c6ca7-312">Defina o `href` valor do atributo para o nome do repositório GitHub com uma barra à direita (por exemplo, `/my-repository/` ).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-312">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="c6ca7-313">No [ Blazor repositório GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), a base `href` é atualizada em publicar pelo arquivo de [ `.github/workflows/main.yml` configuração](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-313">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="c6ca7-314">O [ Blazor repositório GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) não é proprietário, mantido ou tem suporte do .net Foundation ou da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-314">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="c6ca7-315">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="c6ca7-315">Host configuration values</span></span>

<span data-ttu-id="c6ca7-316">os [ Blazor WebAssembly aplicativos](xref:blazor/hosting-models#blazor-webassembly) podem aceitar os seguintes valores de configuração de host como argumentos de linha de comando em tempo de execução no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-316">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="c6ca7-317">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="c6ca7-317">Content root</span></span>

<span data-ttu-id="c6ca7-318">O `--contentroot` argumento define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo ([raiz de conteúdo](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-318">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="c6ca7-319">Nos exemplos a seguir, `/content-root-path` é o caminho raiz do conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-319">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="c6ca7-320">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-320">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c6ca7-321">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-321">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="c6ca7-322">Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-322">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c6ca7-323">Esta configuração é usada quando o aplicativo é executado com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-323">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="c6ca7-324">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-324">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c6ca7-325">Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-325">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="c6ca7-326">Caminho base</span><span class="sxs-lookup"><span data-stu-id="c6ca7-326">Path base</span></span>

<span data-ttu-id="c6ca7-327">O `--pathbase` argumento define o caminho base do aplicativo para um aplicativo executado localmente com um caminho de URL relativo não raiz (a `<base>` marca `href` é definida para um caminho diferente `/` de para preparo e produção).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-327">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="c6ca7-328">Nos exemplos a seguir, `/relative-URL-path` é o caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-328">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="c6ca7-329">Para obter mais informações, consulte [caminho base do aplicativo](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-329">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c6ca7-330">Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-330">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="c6ca7-331">Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/">` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-331">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="c6ca7-332">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-332">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c6ca7-333">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-333">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="c6ca7-334">Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-334">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c6ca7-335">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-335">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="c6ca7-336">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-336">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c6ca7-337">Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-337">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="c6ca7-338">URLs</span><span class="sxs-lookup"><span data-stu-id="c6ca7-338">URLs</span></span>

<span data-ttu-id="c6ca7-339">O argumento `--urls` define os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-339">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="c6ca7-340">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-340">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c6ca7-341">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-341">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="c6ca7-342">Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-342">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c6ca7-343">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-343">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="c6ca7-344">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-344">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c6ca7-345">Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-345">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="c6ca7-346">Configurar o cortador</span><span class="sxs-lookup"><span data-stu-id="c6ca7-346">Configure the Trimmer</span></span>

<span data-ttu-id="c6ca7-347">Blazor executa a corte de IL (linguagem intermediária) em cada Build de versão para remover o IL desnecessário dos assemblies de saída.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-347">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="c6ca7-348">Para obter mais informações, consulte <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-348">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="c6ca7-349">Configurar o vinculador</span><span class="sxs-lookup"><span data-stu-id="c6ca7-349">Configure the Linker</span></span>

<span data-ttu-id="c6ca7-350">Blazor executa a vinculação de IL (linguagem intermediária) em cada Build de versão para remover o IL desnecessário dos assemblies de saída.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-350">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="c6ca7-351">Para obter mais informações, consulte <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-351">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="c6ca7-352">Carregamento de recurso de inicialização personalizada</span><span class="sxs-lookup"><span data-stu-id="c6ca7-352">Custom boot resource loading</span></span>

<span data-ttu-id="c6ca7-353">Um Blazor WebAssembly aplicativo pode ser inicializado com a `loadBootResource` função para substituir o mecanismo de carregamento do recurso de inicialização interno.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-353">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="c6ca7-354">Use `loadBootResource` para os seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-354">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="c6ca7-355">Permitir que os usuários carreguem recursos estáticos, como dados de fuso horário ou `dotnet.wasm` de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-355">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="c6ca7-356">Carregue assemblies compactados usando uma solicitação HTTP e descompacte-os no cliente para hosts que não dão suporte à busca de conteúdo compactado do servidor.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-356">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="c6ca7-357">Recursos de alias para um nome diferente redirecionando cada `fetch` solicitação para um novo nome.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-357">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="c6ca7-358">`loadBootResource` os parâmetros aparecem na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-358">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="c6ca7-359">Parâmetro</span><span class="sxs-lookup"><span data-stu-id="c6ca7-359">Parameter</span></span>    | <span data-ttu-id="c6ca7-360">Descrição</span><span class="sxs-lookup"><span data-stu-id="c6ca7-360">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="c6ca7-361">Tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-361">The type of the resource.</span></span> <span data-ttu-id="c6ca7-362">Tipos permissiváveis: `assembly` ,, `pdb` , `dotnetjs` `dotnetwasm` , `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="c6ca7-362">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="c6ca7-363">O nome do recurso.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-363">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="c6ca7-364">O URI relativo ou absoluto do recurso.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-364">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="c6ca7-365">A cadeia de caracteres de integridade que representa o conteúdo esperado na resposta.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-365">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="c6ca7-366">`loadBootResource` retorna qualquer um dos seguintes para substituir o processo de carregamento:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-366">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="c6ca7-367">Cadeia de caracteres de URI.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-367">URI string.</span></span> <span data-ttu-id="c6ca7-368">No exemplo a seguir ( `wwwroot/index.html` ), os seguintes arquivos são atendidos de uma CDN em `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="c6ca7-368">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="c6ca7-369">Dados de fuso horário</span><span class="sxs-lookup"><span data-stu-id="c6ca7-369">Timezone data</span></span>

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

* <span data-ttu-id="c6ca7-370">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-370">`Promise<Response>`.</span></span> <span data-ttu-id="c6ca7-371">Passe o `integrity` parâmetro em um cabeçalho para manter o comportamento de verificação de integridade padrão.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-371">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="c6ca7-372">O exemplo a seguir ( `wwwroot/index.html` ) adiciona um cabeçalho HTTP personalizado às solicitações de saída e passa o `integrity` parâmetro para a `fetch` chamada:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-372">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="c6ca7-373">`null`/`undefined`, o que resulta no comportamento de carregamento padrão.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-373">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="c6ca7-374">As fontes externas devem retornar os cabeçalhos CORS necessários para que os navegadores permitam o carregamento de recursos entre origens.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-374">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="c6ca7-375">O CDNs geralmente fornece os cabeçalhos necessários por padrão.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-375">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="c6ca7-376">Você só precisa especificar tipos para comportamentos personalizados.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-376">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="c6ca7-377">Tipos não especificados para `loadBootResource` são carregados pela estrutura de acordo com seus comportamentos de carregamento padrão.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-377">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="c6ca7-378">Alterar a extensão do nome de arquivo de arquivos DLL</span><span class="sxs-lookup"><span data-stu-id="c6ca7-378">Change the filename extension of DLL files</span></span>

<span data-ttu-id="c6ca7-379">Caso você tenha a necessidade de alterar as extensões de nome de arquivo dos arquivos publicados do aplicativo `.dll` , siga as orientações nesta seção.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-379">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="c6ca7-380">Depois de publicar o aplicativo, use um script de shell ou o pipeline de compilação DevOps para renomear `.dll` arquivos para usar uma extensão de arquivo diferente.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-380">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="c6ca7-381">Direcione os `.dll` arquivos no `wwwroot` diretório da saída publicada do aplicativo (por exemplo, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-381">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="c6ca7-382">Nos exemplos a seguir, `.dll` os arquivos são renomeados para usar a `.bin` extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-382">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="c6ca7-383">No Windows:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-383">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="c6ca7-384">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-384">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="c6ca7-385">No Linux ou macOS:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-385">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="c6ca7-386">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-386">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="c6ca7-387">Para usar uma extensão de arquivo diferente da `.bin` , substitua `.bin` os comandos anteriores.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-387">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="c6ca7-388">Para resolver os arquivos compactados `blazor.boot.json.gz` `blazor.boot.json.br` , adote uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-388">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="c6ca7-389">Remova os arquivos compactados `blazor.boot.json.gz` `blazor.boot.json.br` .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-389">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="c6ca7-390">A compactação está desabilitada com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-390">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="c6ca7-391">Recompacte o `blazor.boot.json` arquivo atualizado.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-391">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="c6ca7-392">As diretrizes anteriores também se aplicam quando os ativos de trabalho do serviço estão em uso.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-392">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="c6ca7-393">Remova ou recompacte `wwwroot/service-worker-assets.js.br` e `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-393">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="c6ca7-394">Caso contrário, as verificações de integridade de arquivo falharão no navegador.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-394">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="c6ca7-395">O exemplo de Windows a seguir usa um script do PowerShell colocado na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-395">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="c6ca7-396">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-396">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="c6ca7-397">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-397">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="c6ca7-398">No arquivo de projeto, o script é executado após a publicação do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-398">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="c6ca7-399">Ao renomear e carregar lentos os mesmos assemblies, consulte as diretrizes em <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-399">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

## <a name="resolve-integrity-check-failures"></a><span data-ttu-id="c6ca7-400">Resolver falhas de verificação de integridade</span><span class="sxs-lookup"><span data-stu-id="c6ca7-400">Resolve integrity check failures</span></span>

<span data-ttu-id="c6ca7-401">Quando Blazor WebAssembly o baixa os arquivos de inicialização de um aplicativo, ele instrui o navegador a executar verificações de integridade nas respostas.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-401">When Blazor WebAssembly downloads an app's startup files, it instructs the browser to perform integrity checks on the responses.</span></span> <span data-ttu-id="c6ca7-402">Ele usa informações no `blazor.boot.json` arquivo para especificar os valores de hash SHA-256 esperados para `.dll` , `.wasm` e outros arquivos.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-402">It uses information in the `blazor.boot.json` file to specify the expected SHA-256 hash values for `.dll`, `.wasm`, and other files.</span></span> <span data-ttu-id="c6ca7-403">Isso é benéfico pelos seguintes motivos:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-403">This is beneficial for the following reasons:</span></span>

* <span data-ttu-id="c6ca7-404">Ele garante que você não faça o risco de carregar um conjunto inconsistente de arquivos, por exemplo, se uma nova implantação for aplicada ao seu servidor Web enquanto o usuário estiver no processo de baixar os arquivos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-404">It ensures you don't risk loading an inconsistent set of files, for example if a new deployment is applied to your web server while the user is in the process of downloading the application files.</span></span> <span data-ttu-id="c6ca7-405">Arquivos inconsistentes podem levar a um comportamento indefinido.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-405">Inconsistent files could lead to undefined behavior.</span></span>
* <span data-ttu-id="c6ca7-406">Ele garante que o navegador do usuário nunca armazene em cache respostas inconsistentes ou inválidas, o que pode impedi-los de iniciar o aplicativo, mesmo se eles atualizarem manualmente a página.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-406">It ensures the user's browser never caches inconsistent or invalid responses, which could prevent them from starting the app even if they manually refresh the page.</span></span>
* <span data-ttu-id="c6ca7-407">Ele torna seguro armazenar em cache as respostas e nem mesmo verificar se há alterações no lado do servidor até que os hashes do SHA-256 sejam alterados, portanto, as cargas de página subsequentes envolvem menos solicitações e são concluídas com muito mais rapidez.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-407">It makes it safe to cache the responses and not even check for server-side changes until the expected SHA-256 hashes themselves change, so subsequent page loads involve fewer requests and complete much faster.</span></span>

<span data-ttu-id="c6ca7-408">Se o servidor Web retornar respostas que não correspondam aos hashes SHA-256 esperados, você verá um erro semelhante ao mostrado a seguir no console do desenvolvedor do navegador:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-408">If your web server returns responses that don't match the expected SHA-256 hashes, you will see an error similar to the following appear in the browser's developer console:</span></span>

```
Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='. The resource has been blocked.
```

<span data-ttu-id="c6ca7-409">Na maioria dos casos, isso *não* é um problema com a verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-409">In most cases, this is *not* a problem with integrity checking itself.</span></span> <span data-ttu-id="c6ca7-410">Em vez disso, isso significa que há algum outro problema, e a verificação de integridade é avisado sobre esse outro problema.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-410">Instead, it means there is some other problem, and the integrity check is warning you about that other problem.</span></span>

### <a name="diagnosing-integrity-problems"></a><span data-ttu-id="c6ca7-411">Diagnosticando problemas de integridade</span><span class="sxs-lookup"><span data-stu-id="c6ca7-411">Diagnosing integrity problems</span></span>

<span data-ttu-id="c6ca7-412">Quando um aplicativo é compilado, o `blazor.boot.json` manifesto gerado descreve os hashes SHA-256 de seus recursos de inicialização (por exemplo,, `.dll` `.wasm` e outros arquivos) no momento em que a saída da compilação é produzida.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-412">When an app is built, the generated `blazor.boot.json` manifest describes the SHA-256 hashes of your boot resources (for example, `.dll`, `.wasm`, and other files) at the time that the build output is produced.</span></span> <span data-ttu-id="c6ca7-413">A verificação de integridade é aprovada, desde que os hashes SHA-256 `blazor.boot.json` correspondam aos arquivos entregues ao navegador.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-413">The integrity check passes as long as the SHA-256 hashes in `blazor.boot.json` match the files delivered to the browser.</span></span>

<span data-ttu-id="c6ca7-414">Motivos comuns pelos quais isso falha são:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-414">Common reasons why this fails are:</span></span>

 * <span data-ttu-id="c6ca7-415">A resposta do servidor Web é um erro (por exemplo, um *404-não encontrado* ou um *erro de servidor interno 500* ) em vez do arquivo solicitado pelo navegador.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-415">The web server's response is an error (for example, a *404 - Not Found* or a *500 - Internal Server Error* ) instead of the file the browser requested.</span></span> <span data-ttu-id="c6ca7-416">Isso é relatado pelo navegador como uma falha de verificação de integridade e não como uma falha de resposta.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-416">This is reported by the browser as an integrity check failure and not as a response failure.</span></span>
 * <span data-ttu-id="c6ca7-417">Algo alterou o conteúdo dos arquivos entre a compilação e a entrega dos arquivos para o navegador.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-417">Something has changed the contents of the files between the build and delivery of the files to the browser.</span></span> <span data-ttu-id="c6ca7-418">Isso pode acontecer:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-418">This might happen:</span></span>
   * <span data-ttu-id="c6ca7-419">Se você ou criar ferramentas, modifique manualmente a saída da compilação.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-419">If you or build tools manually modify the build output.</span></span>
   * <span data-ttu-id="c6ca7-420">Se algum aspecto do processo de implantação tiver modificado os arquivos.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-420">If some aspect of the deployment process modified the files.</span></span> <span data-ttu-id="c6ca7-421">Por exemplo, se você usar um mecanismo de implantação baseado em git, tenha em mente que o Git converte de forma transparente as terminações de linha no estilo do Windows para as terminações de linha em estilo UNIX se você confirmar arquivos no Windows e verificá-los no Linux.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-421">For example if you use a Git-based deployment mechanism, bear in mind that Git transparently converts Windows-style line endings to Unix-style line endings if you commit files on Windows and check them out on Linux.</span></span> <span data-ttu-id="c6ca7-422">Alterar as terminações de linha de arquivo altera os hashes SHA-256.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-422">Changing file line endings change the SHA-256 hashes.</span></span> <span data-ttu-id="c6ca7-423">Para evitar esse problema, considere [usar `.gitattributes` para tratar artefatos de compilação como `binary` arquivos](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-423">To avoid this problem, consider [using `.gitattributes` to treat build artifacts as `binary` files](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span></span>
   * <span data-ttu-id="c6ca7-424">O servidor Web modifica o conteúdo do arquivo como parte do fornecimento deles.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-424">The web server modifies the file contents as part of serving them.</span></span> <span data-ttu-id="c6ca7-425">Por exemplo, algumas CDNs (redes de distribuição de conteúdo) tentam automaticamente [reduzir](xref:client-side/bundling-and-minification#minification) HTML, modificando-as.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-425">For example, some content distribution networks (CDNs) automatically attempt to [minify](xref:client-side/bundling-and-minification#minification) HTML, thereby modifying it.</span></span> <span data-ttu-id="c6ca7-426">Talvez seja necessário desabilitar esses recursos.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-426">You may need to disable such features.</span></span>

<span data-ttu-id="c6ca7-427">Para diagnosticar quais delas se aplicam no seu caso:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-427">To diagnose which of these applies in your case:</span></span>

 1. <span data-ttu-id="c6ca7-428">Observe qual arquivo está disparando o erro lendo a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-428">Note which file is triggering the error by reading the error message.</span></span>
 1. <span data-ttu-id="c6ca7-429">Abra as ferramentas de desenvolvedor do navegador e procure na guia *rede* . Se necessário, recarregue a página para ver a lista de solicitações e respostas.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-429">Open your browser's developer tools and look in the *Network* tab. If necessary, reload the page to see the list of requests and responses.</span></span> <span data-ttu-id="c6ca7-430">Localize o arquivo que está disparando o erro nessa lista.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-430">Find the file that is triggering the error in that list.</span></span>
 1. <span data-ttu-id="c6ca7-431">Verifique o código de status HTTP na resposta.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-431">Check the HTTP status code in the response.</span></span> <span data-ttu-id="c6ca7-432">Se o servidor retornar algo diferente de *200-OK* (ou outro código de status 2xx), você terá um problema no lado do servidor para diagnosticar.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-432">If the server returns anything other than *200 - OK* (or another 2xx status code), then you have a server-side problem to diagnose.</span></span> <span data-ttu-id="c6ca7-433">Por exemplo, o código de status 403 significa que há um problema de autorização, enquanto o código de status 500 significa que o servidor está falhando de maneira não especificada.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-433">For example, status code 403 means there's an authorization problem, whereas status code 500 means the server is failing in an unspecified manner.</span></span> <span data-ttu-id="c6ca7-434">Consulte os logs do lado do servidor para diagnosticar e corrigir o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-434">Consult server-side logs to diagnose and fix the app.</span></span>
 1. <span data-ttu-id="c6ca7-435">Se o código de status for *200-OK* para o recurso, examine o conteúdo da resposta nas ferramentas de desenvolvedor do navegador e verifique se o conteúdo corresponde aos dados esperados.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-435">If the status code is *200 - OK* for the resource, look at the response content in browser's developer tools and check that the content matchs up with the data expected.</span></span> <span data-ttu-id="c6ca7-436">Por exemplo, um problema comum é configurar incorretamente o roteamento para que as solicitações retornem seus `index.html` dados mesmo para outros arquivos.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-436">For example, a common problem is to misconfigure routing so that requests return your `index.html` data even for other files.</span></span> <span data-ttu-id="c6ca7-437">Verifique se as respostas a `.wasm` solicitações são binários de Webassembly e se `.dll` as respostas a solicitações são binários de assembly .net.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-437">Make sure that responses to `.wasm` requests are WebAssembly binaries and that responses to `.dll` requests are .NET assembly binaries.</span></span> <span data-ttu-id="c6ca7-438">Caso contrário, você tem um problema de roteamento no lado do servidor para diagnosticar.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-438">If not, you have a server-side routing problem to diagnose.</span></span>

<span data-ttu-id="c6ca7-439">Se você confirmar que o servidor está retornando os dados corretos do plausível, deve haver outra coisa modificando o conteúdo entre a compilação e a entrega do arquivo.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-439">If you confirm that the server is returning plausibly correct data, there must be something else modifying the contents in between build and delivery of the file.</span></span> <span data-ttu-id="c6ca7-440">Para investigar isso:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-440">To investigate this:</span></span>

 * <span data-ttu-id="c6ca7-441">Examine o mecanismo de implantação e ferramentas de compilação caso eles estejam modificando arquivos depois que os arquivos forem criados.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-441">Examine the build toolchain and deployment mechanism in case they're modifying files after the files are built.</span></span> <span data-ttu-id="c6ca7-442">Um exemplo disso é quando o Git transforma as terminações de linha de arquivo, conforme descrito anteriormente.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-442">An example of this is when Git transforms file line endings, as described earlier.</span></span>
 * <span data-ttu-id="c6ca7-443">Examine o servidor Web ou a configuração da CDN caso eles estejam configurados para modificar as respostas dinamicamente (por exemplo, tentando reduzir HTML).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-443">Examine the web server or CDN configuration in case they're set up to modify responses dynamically (for example, trying to minify HTML).</span></span> <span data-ttu-id="c6ca7-444">É bom que o servidor Web implemente a compactação HTTP (por exemplo, retornando `content-encoding: br` ou `content-encoding: gzip` ), já que isso não afeta o resultado após a descompactação.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-444">It's fine for the web server to implement HTTP compression (for example, returning `content-encoding: br` or `content-encoding: gzip`), since this doesn't affect the result after decompression.</span></span> <span data-ttu-id="c6ca7-445">No entanto, *não* há problema para o servidor Web modificar os dados descompactados.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-445">However, it's *not* fine for the web server to modify the uncompressed data.</span></span>

### <a name="disable-integrity-checking-for-non-pwa-apps"></a><span data-ttu-id="c6ca7-446">Desabilitar verificação de integridade para aplicativos não PWA</span><span class="sxs-lookup"><span data-stu-id="c6ca7-446">Disable integrity checking for non-PWA apps</span></span>

<span data-ttu-id="c6ca7-447">Na maioria dos casos, não desabilite a verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-447">In most cases, don't disable integrity checking.</span></span> <span data-ttu-id="c6ca7-448">Desabilitar a verificação de integridade não resolve o problema subjacente que causou as respostas inesperadas e resulta na perda dos benefícios listados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-448">Disabling integrity checking doesn't solve the underlying problem that has caused the unexpected responses and results in losing the benefits listed earlier.</span></span>

<span data-ttu-id="c6ca7-449">Pode haver casos em que o servidor Web não pode ser confiado para retornar respostas consistentes e você não tem a opção de desabilitar as verificações de integridade.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-449">There may be cases where the web server can't be relied upon to return consistent responses, and you have no choice but to disable integrity checks.</span></span> <span data-ttu-id="c6ca7-450">Para desabilitar as verificações de integridade, adicione o seguinte a um grupo de propriedades no Blazor WebAssembly arquivo do projeto `.csproj` :</span><span class="sxs-lookup"><span data-stu-id="c6ca7-450">To disable integrity checks, add the following to a property group in the Blazor WebAssembly project's `.csproj` file:</span></span>

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

<span data-ttu-id="c6ca7-451">`BlazorCacheBootResources` também desabilita Blazor o comportamento padrão de armazenar em cache o `.dll` , o `.wasm` e outros arquivos com base em seus hashes SHA-256 porque a propriedade indica que os hashes SHA-256 não podem ser dependentes quanto à exatidão.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-451">`BlazorCacheBootResources` also disables Blazor's default behavior of caching the `.dll`, `.wasm`, and other files based on their SHA-256 hashes because the property indicates that the SHA-256 hashes can't be relied upon for correctness.</span></span> <span data-ttu-id="c6ca7-452">Mesmo com essa configuração, o cache HTTP normal do navegador ainda pode armazenar em cache esses arquivos, mas se isso ocorre ou não, depende da configuração do servidor Web e dos `cache-control` cabeçalhos que ele serve.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-452">Even with this setting, the browser's normal HTTP cache may still cache those files, but whether or not this happens depends on your web server configuration and the `cache-control` headers that it serves.</span></span>

> [!NOTE]
> <span data-ttu-id="c6ca7-453">A `BlazorCacheBootResources` propriedade não desabilita verificações de integridade para [aplicativos Web progressivos (PWAs)](xref:blazor/progressive-web-app).</span><span class="sxs-lookup"><span data-stu-id="c6ca7-453">The `BlazorCacheBootResources` property doesn't disable integrity checks for [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app).</span></span> <span data-ttu-id="c6ca7-454">Para obter diretrizes referentes ao PWAs, consulte a seção [Desabilitar verificação de integridade para PWAs](#disable-integrity-checking-for-pwas) .</span><span class="sxs-lookup"><span data-stu-id="c6ca7-454">For guidance pertaining to PWAs, see the [Disable integrity checking for PWAs](#disable-integrity-checking-for-pwas) section.</span></span>

### <a name="disable-integrity-checking-for-pwas"></a><span data-ttu-id="c6ca7-455">Desabilitar verificação de integridade para PWAs</span><span class="sxs-lookup"><span data-stu-id="c6ca7-455">Disable integrity checking for PWAs</span></span>

<span data-ttu-id="c6ca7-456">Blazoro modelo do aplicativo Web progressivo (PWA) contém um `service-worker.published.js` arquivo sugerido que é responsável por buscar e armazenar arquivos de aplicativo para uso offline.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-456">Blazor's Progressive Web Application (PWA) template contains a suggested `service-worker.published.js` file that's responsible for fetching and storing application files for offline use.</span></span> <span data-ttu-id="c6ca7-457">Esse é um processo separado do mecanismo de inicialização de aplicativo normal e tem sua própria lógica de verificação de integridade separada.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-457">This is a separate process from the normal app startup mechanism and has its own separate integrity checking logic.</span></span>

<span data-ttu-id="c6ca7-458">Dentro do `service-worker.published.js` arquivo, a linha a seguir está presente:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-458">Inside the `service-worker.published.js` file, following line is present:</span></span>

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

<span data-ttu-id="c6ca7-459">Para desabilitar a verificação de integridade, remova o `integrity` parâmetro alterando a linha para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="c6ca7-459">To disable integrity checking, remove the `integrity` parameter by changing the line to the following:</span></span>

```javascript
.map(asset => new Request(asset.url));
```

<span data-ttu-id="c6ca7-460">Novamente, desabilitar a verificação de integridade significa que você perde as garantias de segurança oferecidas pela verificação de integridade.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-460">Again, disabling integrity checking means that you lose the safety guarantees offered by integrity checking.</span></span> <span data-ttu-id="c6ca7-461">Por exemplo, há um risco de que, se o navegador do usuário estiver armazenando o aplicativo em cache no momento exato em que você implantar uma nova versão, ele poderá armazenar em cache alguns arquivos da implantação antiga e alguns da nova implantação.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-461">For example, there is a risk that if the user's browser is caching the app at the exact moment that you deploy a new version, it could cache some files from the old deployment and some from the new deployment.</span></span> <span data-ttu-id="c6ca7-462">Se isso acontecer, o aplicativo ficará preso em um estado rompido até que você implante uma atualização adicional.</span><span class="sxs-lookup"><span data-stu-id="c6ca7-462">If that happens, the app becomes stuck in a broken state until you deploy a further update.</span></span>
