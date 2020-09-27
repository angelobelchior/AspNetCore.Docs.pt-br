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
ms.openlocfilehash: 3436620123618ab32daa44c4a37057aaadb89563
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393685"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="6de04-103">Hospedar e implantar ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6de04-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="6de04-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)e [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="6de04-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="6de04-105">Com o [ Blazor WebAssembly modelo de hospedagem](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="6de04-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="6de04-106">O Blazor aplicativo, suas dependências e o tempo de execução do .NET são baixados para o navegador em paralelo.</span><span class="sxs-lookup"><span data-stu-id="6de04-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="6de04-107">O aplicativo é executado diretamente no thread da interface do usuário do navegador.</span><span class="sxs-lookup"><span data-stu-id="6de04-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="6de04-108">Há suporte para as seguintes estratégias de implantação:</span><span class="sxs-lookup"><span data-stu-id="6de04-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="6de04-109">O Blazor aplicativo é servido por um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6de04-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="6de04-110">Esta estratégia é abordada na seção [Implantação hospedada com o ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="6de04-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="6de04-111">O Blazor aplicativo é colocado em um servidor Web ou serviço de hospedagem estática, em que o .net não é usado para servir o Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6de04-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="6de04-112">Essa estratégia é abordada na seção [implantação autônoma](#standalone-deployment) , que inclui informações sobre como hospedar um Blazor WebAssembly aplicativo como um subaplicativo do IIS.</span><span class="sxs-lookup"><span data-stu-id="6de04-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="6de04-113">Compactação</span><span class="sxs-lookup"><span data-stu-id="6de04-113">Compression</span></span>

<span data-ttu-id="6de04-114">Quando um Blazor WebAssembly aplicativo é publicado, a saída é compactada estaticamente durante a publicação para reduzir o tamanho do aplicativo e remover a sobrecarga de compactação de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="6de04-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="6de04-115">Os seguintes algoritmos de compactação são usados:</span><span class="sxs-lookup"><span data-stu-id="6de04-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="6de04-116">[Brotli](https://tools.ietf.org/html/rfc7932) (nível mais alto)</span><span class="sxs-lookup"><span data-stu-id="6de04-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="6de04-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="6de04-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="6de04-118">Blazor o se baseia no host para o fornecer os arquivos compactados apropriados.</span><span class="sxs-lookup"><span data-stu-id="6de04-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="6de04-119">Ao usar um projeto ASP.NET Core hospedado, o projeto host é capaz de executar a negociação de conteúdo e fornecer os arquivos compactados estaticamente.</span><span class="sxs-lookup"><span data-stu-id="6de04-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="6de04-120">Ao hospedar um Blazor WebAssembly aplicativo autônomo, um trabalho adicional pode ser necessário para garantir que arquivos compactados estaticamente sejam atendidos:</span><span class="sxs-lookup"><span data-stu-id="6de04-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="6de04-121">Para `web.config` a configuração de compactação do IIS, consulte a seção [IIS: Brotli e a compactação Gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="6de04-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="6de04-122">Ao hospedar soluções de hospedagem estática que não dão suporte à negociação de conteúdo de arquivo compactado estaticamente, como páginas do GitHub, considere configurar o aplicativo para buscar e decodificar arquivos compactados Brotli:</span><span class="sxs-lookup"><span data-stu-id="6de04-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="6de04-123">Obtenha o decodificador Brotli do JavaScript do [repositório GitHub do Google/Brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="6de04-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="6de04-124">A partir de setembro de 2020, o arquivo de decodificador é nomeado `decode.js` e encontrado na [ `js` pasta](https://github.com/google/brotli/tree/master/js)do repositório.</span><span class="sxs-lookup"><span data-stu-id="6de04-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="6de04-125">Uma regressão está presente na versão reduzidos do `decode.js` script ( `decode.min.js` ) no [repositório GitHub do Google/brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="6de04-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="6de04-126">Reduzir o script por conta própria ou use o [pacote NPM](https://www.npmjs.com/package/brotli) até que a janela de problema [. BrotliDecode não esteja definida em decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) seja resolvida.</span><span class="sxs-lookup"><span data-stu-id="6de04-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="6de04-127">O código de exemplo nesta seção usa a versão **unminified** do script.</span><span class="sxs-lookup"><span data-stu-id="6de04-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="6de04-128">Atualize o aplicativo para usar o decodificador.</span><span class="sxs-lookup"><span data-stu-id="6de04-128">Update the app to use the decoder.</span></span> <span data-ttu-id="6de04-129">Altere a marcação dentro da `<body>` marca de fechamento `wwwroot/index.html` para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="6de04-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
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
 
<span data-ttu-id="6de04-130">Para desabilitar a compactação, adicione a `BlazorEnableCompression` Propriedade MSBuild ao arquivo de projeto do aplicativo e defina o valor como `false` :</span><span class="sxs-lookup"><span data-stu-id="6de04-130">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="6de04-131">A `BlazorEnableCompression` propriedade pode ser passada para o [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando com a seguinte sintaxe em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="6de04-131">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="6de04-132">Reescrever as URLs para obter o roteamento correto</span><span class="sxs-lookup"><span data-stu-id="6de04-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="6de04-133">O roteamento de solicitações para componentes de página em um Blazor WebAssembly aplicativo não é tão simples quanto o roteamento de solicitações em um Blazor Server aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="6de04-133">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="6de04-134">Considere um Blazor WebAssembly aplicativo com dois componentes:</span><span class="sxs-lookup"><span data-stu-id="6de04-134">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="6de04-135">`Main.razor`: Carrega na raiz do aplicativo e contém um link para o `About` componente ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="6de04-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="6de04-136">`About.razor`: `About` componente.</span><span class="sxs-lookup"><span data-stu-id="6de04-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="6de04-137">Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="6de04-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="6de04-138">O navegador faz uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="6de04-138">The browser makes a request.</span></span>
1. <span data-ttu-id="6de04-139">A página padrão é retornada, o que geralmente é `index.html` .</span><span class="sxs-lookup"><span data-stu-id="6de04-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="6de04-140">`index.html` Inicializa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6de04-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="6de04-141">Blazoro roteador do é carregado e o Razor `Main` componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="6de04-141">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="6de04-142">Na página principal, selecionar o link para o `About` componente funciona no cliente, pois o Blazor roteador interrompe o navegador de fazer uma solicitação na Internet para `www.contoso.com` `About` e serve o componente renderizado em `About` si.</span><span class="sxs-lookup"><span data-stu-id="6de04-142">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="6de04-143">Todas as solicitações de pontos de extremidade internos *no Blazor WebAssembly aplicativo* funcionam da mesma maneira: as solicitações não disparam solicitações baseadas em navegador para recursos hospedados no servidor na Internet.</span><span class="sxs-lookup"><span data-stu-id="6de04-143">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="6de04-144">O roteador trata das solicitações internamente.</span><span class="sxs-lookup"><span data-stu-id="6de04-144">The router handles the requests internally.</span></span>

<span data-ttu-id="6de04-145">Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="6de04-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="6de04-146">Este recurso não existe no host do aplicativo na Internet; portanto, uma resposta *404 – Não Encontrado* é retornada.</span><span class="sxs-lookup"><span data-stu-id="6de04-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="6de04-147">Como os navegadores fazem solicitações para hosts baseados na Internet para páginas do lado do cliente, servidores Web e serviços de hospedagem devem regravar todas as solicitações de recursos não fisicamente no servidor para a `index.html` página.</span><span class="sxs-lookup"><span data-stu-id="6de04-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="6de04-148">Quando `index.html` é retornado, o roteador do aplicativo Blazor assume o failover e responde com o recurso correto.</span><span class="sxs-lookup"><span data-stu-id="6de04-148">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="6de04-149">Ao implantar em um servidor IIS, você pode usar o módulo de reescrita de URL com o arquivo publicado do aplicativo `web.config` .</span><span class="sxs-lookup"><span data-stu-id="6de04-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="6de04-150">Para obter mais informações, consulte a seção [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="6de04-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="6de04-151">Implantação hospedada com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6de04-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="6de04-152">Uma *implantação hospedada* serve o Blazor WebAssembly aplicativo para os navegadores de um [aplicativo ASP.NET Core](xref:index) executado em um servidor Web.</span><span class="sxs-lookup"><span data-stu-id="6de04-152">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="6de04-153">O Blazor WebAssembly aplicativo cliente é publicado na `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` pasta do aplicativo de servidor, juntamente com quaisquer outros ativos da Web estáticos do aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="6de04-153">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="6de04-154">Os dois aplicativos são implantados juntos.</span><span class="sxs-lookup"><span data-stu-id="6de04-154">The two apps are deployed together.</span></span> <span data-ttu-id="6de04-155">É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6de04-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="6de04-156">Para uma implantação hospedada, o Visual Studio inclui o modelo de projeto de \*\* Blazor WebAssembly aplicativo\*\* ( `blazorwasm` modelo ao usar o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando) com a **`Hosted`** opção selecionada ( `-ho|--hosted` ao usar o `dotnet new` comando).</span><span class="sxs-lookup"><span data-stu-id="6de04-156">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="6de04-157">Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="6de04-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="6de04-158">Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="6de04-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="6de04-159">Implantação hospedada com vários Blazor WebAssembly aplicativos</span><span class="sxs-lookup"><span data-stu-id="6de04-159">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="6de04-160">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="6de04-160">App configuration</span></span>

<span data-ttu-id="6de04-161">Para configurar uma solução hospedada Blazor para atender a vários Blazor WebAssembly aplicativos:</span><span class="sxs-lookup"><span data-stu-id="6de04-161">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="6de04-162">Use uma solução hospedada existente Blazor ou crie uma nova solução a partir do Blazor modelo de projeto hospedado.</span><span class="sxs-lookup"><span data-stu-id="6de04-162">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="6de04-163">No arquivo de projeto do aplicativo cliente, adicione uma `<StaticWebAssetBasePath>` propriedade ao `<PropertyGroup>` com um valor de `FirstApp` para definir o caminho base para os ativos estáticos do projeto:</span><span class="sxs-lookup"><span data-stu-id="6de04-163">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="6de04-164">Adicione um segundo aplicativo cliente à solução:</span><span class="sxs-lookup"><span data-stu-id="6de04-164">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="6de04-165">Adicione uma pasta chamada `SecondClient` à pasta da solução.</span><span class="sxs-lookup"><span data-stu-id="6de04-165">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="6de04-166">Crie um Blazor WebAssembly aplicativo chamado `SecondBlazorApp.Client` na `SecondClient` pasta do modelo de Blazor WebAssembly projeto.</span><span class="sxs-lookup"><span data-stu-id="6de04-166">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="6de04-167">No arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="6de04-167">In the app's project file:</span></span>

    * <span data-ttu-id="6de04-168">Adicione uma `<StaticWebAssetBasePath>` propriedade ao `<PropertyGroup>` com um valor de `SecondApp` :</span><span class="sxs-lookup"><span data-stu-id="6de04-168">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="6de04-169">Adicione uma referência de projeto ao `Shared` projeto:</span><span class="sxs-lookup"><span data-stu-id="6de04-169">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="6de04-170">O espaço reservado `{SOLUTION NAME}` é o nome da solução.</span><span class="sxs-lookup"><span data-stu-id="6de04-170">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="6de04-171">No arquivo de projeto do aplicativo do servidor, crie uma referência de projeto para o aplicativo cliente adicionado:</span><span class="sxs-lookup"><span data-stu-id="6de04-171">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="6de04-172">No arquivo do aplicativo do servidor `Properties/launchSettings.json` , configure o `applicationUrl` do perfil Kestrel ( `{SOLUTION NAME}.Server` ) para acessar os aplicativos cliente nas portas 5001 e 5002:</span><span class="sxs-lookup"><span data-stu-id="6de04-172">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="6de04-173">No método do aplicativo do servidor `Startup.Configure` ( `Startup.cs` ), remova as seguintes linhas, que aparecem após a chamada para <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :</span><span class="sxs-lookup"><span data-stu-id="6de04-173">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

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

  <span data-ttu-id="6de04-174">Adicione middleware que mapeia solicitações para os aplicativos cliente.</span><span class="sxs-lookup"><span data-stu-id="6de04-174">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="6de04-175">O exemplo a seguir configura o middleware para ser executado quando:</span><span class="sxs-lookup"><span data-stu-id="6de04-175">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="6de04-176">A porta de solicitação é 5001 para o aplicativo cliente original ou 5002 para o aplicativo cliente adicionado.</span><span class="sxs-lookup"><span data-stu-id="6de04-176">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="6de04-177">O host de solicitação é `firstapp.com` para o aplicativo cliente original ou `secondapp.com` para o aplicativo cliente adicionado.</span><span class="sxs-lookup"><span data-stu-id="6de04-177">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="6de04-178">O exemplo mostrado nesta seção requer configuração adicional para o:</span><span class="sxs-lookup"><span data-stu-id="6de04-178">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="6de04-179">Acessar os aplicativos em domínios de host de exemplo `firstapp.com` e `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="6de04-179">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="6de04-180">Certificados para os aplicativos cliente habilitarem a segurança TLS (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6de04-180">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="6de04-181">A configuração necessária está além do escopo deste artigo e depende de como a solução está hospedada.</span><span class="sxs-lookup"><span data-stu-id="6de04-181">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="6de04-182">Para obter mais informações, consulte os [artigos sobre host e implantar](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="6de04-182">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="6de04-183">Coloque o código a seguir onde as linhas foram removidas anteriormente:</span><span class="sxs-lookup"><span data-stu-id="6de04-183">Place the following code where the lines were removed earlier:</span></span>

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

* <span data-ttu-id="6de04-184">No controlador de previsão do tempo do aplicativo do servidor ( `Controllers/WeatherForecastController.cs` ), substitua a rota existente ( `[Route("[controller]")]` ) `WeatherForecastController` por pelas seguintes rotas:</span><span class="sxs-lookup"><span data-stu-id="6de04-184">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="6de04-185">O middleware adicionado ao método do aplicativo do servidor `Startup.Configure` anteriormente modifica as solicitações de entrada para `/WeatherForecast` `/FirstApp/WeatherForecast` ou `/SecondApp/WeatherForecast` dependendo da porta (5001/5002) ou do domínio ( `firstapp.com` / `secondapp.com` ).</span><span class="sxs-lookup"><span data-stu-id="6de04-185">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="6de04-186">As rotas de controlador anteriores são necessárias para retornar dados meteorológicos do aplicativo de servidor para os aplicativos cliente.</span><span class="sxs-lookup"><span data-stu-id="6de04-186">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="6de04-187">Ativos estáticos e bibliotecas de classes</span><span class="sxs-lookup"><span data-stu-id="6de04-187">Static assets and class libraries</span></span>

<span data-ttu-id="6de04-188">Use as seguintes abordagens para ativos estáticos:</span><span class="sxs-lookup"><span data-stu-id="6de04-188">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="6de04-189">Quando o ativo estiver na pasta do aplicativo cliente `wwwroot` , forneça seus caminhos normalmente:</span><span class="sxs-lookup"><span data-stu-id="6de04-189">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="6de04-190">Quando o ativo estiver na `wwwroot` pasta de uma [ Razor biblioteca de classes (RCL)](xref:blazor/components/class-libraries), referencie o ativo estático no aplicativo cliente de acordo com as diretrizes no [artigo RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="6de04-190">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6de04-191">Os componentes fornecidos a um aplicativo cliente por uma biblioteca de classes são referenciados normalmente.</span><span class="sxs-lookup"><span data-stu-id="6de04-191">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="6de04-192">Se algum componente exigir folhas de estilo ou arquivos JavaScript, use uma das abordagens a seguir para obter os ativos estáticos:</span><span class="sxs-lookup"><span data-stu-id="6de04-192">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="6de04-193">O arquivo do aplicativo cliente `wwwroot/index.html` pode vincular ( `<link>` ) aos ativos estáticos.</span><span class="sxs-lookup"><span data-stu-id="6de04-193">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="6de04-194">O componente pode usar o [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) da estrutura para obter os ativos estáticos.</span><span class="sxs-lookup"><span data-stu-id="6de04-194">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="6de04-195">As abordagens anteriores são demonstradas nos exemplos a seguir.</span><span class="sxs-lookup"><span data-stu-id="6de04-195">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6de04-196">Os componentes fornecidos a um aplicativo cliente por uma biblioteca de classes são referenciados normalmente.</span><span class="sxs-lookup"><span data-stu-id="6de04-196">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="6de04-197">Se algum componente exigir folhas de estilo ou arquivos JavaScript, o arquivo do aplicativo cliente `wwwroot/index.html` deverá incluir os links de ativo estático corretos.</span><span class="sxs-lookup"><span data-stu-id="6de04-197">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="6de04-198">Essas abordagens são demonstradas nos exemplos a seguir.</span><span class="sxs-lookup"><span data-stu-id="6de04-198">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="6de04-199">Adicione o componente a seguir `Jeep` a um dos aplicativos cliente.</span><span class="sxs-lookup"><span data-stu-id="6de04-199">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="6de04-200">O `Jeep` componente usa:</span><span class="sxs-lookup"><span data-stu-id="6de04-200">The `Jeep` component uses:</span></span>

* <span data-ttu-id="6de04-201">Uma imagem da pasta do aplicativo cliente `wwwroot` ( `jeep-cj.png` ).</span><span class="sxs-lookup"><span data-stu-id="6de04-201">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="6de04-202">Uma imagem de uma pasta de [ Razor biblioteca de componentes](xref:blazor/components/class-libraries) () adicionada () `JeepImage` `wwwroot` `jeep-yj.png` .</span><span class="sxs-lookup"><span data-stu-id="6de04-202">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="6de04-203">O componente de exemplo ( `Component1` ) é criado automaticamente pelo modelo de projeto RCL quando a `JeepImage` biblioteca é adicionada à solução.</span><span class="sxs-lookup"><span data-stu-id="6de04-203">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

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
> <span data-ttu-id="6de04-204">Não **publique imagens** de veículos publicamente, a menos que você tenha as imagens.</span><span class="sxs-lookup"><span data-stu-id="6de04-204">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="6de04-205">Caso contrário, você arriscará a violação de direitos autorais.</span><span class="sxs-lookup"><span data-stu-id="6de04-205">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6de04-206">A imagem da biblioteca `jeep-yj.png` também pode ser adicionada ao componente da biblioteca `Component1` ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="6de04-206">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="6de04-207">Para fornecer a `my-component` classe CSS à página do aplicativo cliente, vincule à folha de estilos da biblioteca usando o [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)da estrutura:</span><span class="sxs-lookup"><span data-stu-id="6de04-207">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

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

<span data-ttu-id="6de04-208">Uma alternativa ao uso do [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) é carregar a folha de estilos do arquivo do aplicativo cliente `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="6de04-208">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="6de04-209">Essa abordagem torna a folha de estilos disponível para todos os componentes no aplicativo cliente:</span><span class="sxs-lookup"><span data-stu-id="6de04-209">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6de04-210">A imagem da biblioteca `jeep-yj.png` também pode ser adicionada ao componente da biblioteca `Component1` ( `Component1.razor` ):</span><span class="sxs-lookup"><span data-stu-id="6de04-210">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

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

<span data-ttu-id="6de04-211">O arquivo do aplicativo cliente `wwwroot/index.html` solicita a folha de estilos da biblioteca com a seguinte `<link>` marca adicionada:</span><span class="sxs-lookup"><span data-stu-id="6de04-211">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="6de04-212">Adicione navegação ao `Jeep` componente no componente do aplicativo cliente `NavMenu` ( `Shared/NavMenu.razor` ):</span><span class="sxs-lookup"><span data-stu-id="6de04-212">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="6de04-213">Para obter mais informações sobre o RCLs, consulte:</span><span class="sxs-lookup"><span data-stu-id="6de04-213">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="6de04-214">Implantação autônoma</span><span class="sxs-lookup"><span data-stu-id="6de04-214">Standalone deployment</span></span>

<span data-ttu-id="6de04-215">Uma *implantação autônoma* serve o Blazor WebAssembly aplicativo como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes.</span><span class="sxs-lookup"><span data-stu-id="6de04-215">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="6de04-216">Qualquer servidor de arquivos estático é capaz de atender ao Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6de04-216">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="6de04-217">Os ativos de implantação autônomo são publicados na `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` pasta.</span><span class="sxs-lookup"><span data-stu-id="6de04-217">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="6de04-218">Serviço de aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="6de04-218">Azure App Service</span></span>

<span data-ttu-id="6de04-219">Blazor WebAssembly os aplicativos podem ser implantados em serviços Azure App no Windows, que hospedam o aplicativo no [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="6de04-219">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="6de04-220">Blazor WebAssemblyAtualmente, não há suporte para a implantação de um aplicativo autônomo no serviço Azure app para Linux.</span><span class="sxs-lookup"><span data-stu-id="6de04-220">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="6de04-221">Uma imagem do servidor Linux para hospedar o aplicativo não está disponível no momento.</span><span class="sxs-lookup"><span data-stu-id="6de04-221">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="6de04-222">O trabalho está em andamento para habilitar esse cenário.</span><span class="sxs-lookup"><span data-stu-id="6de04-222">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="6de04-223">IIS</span><span class="sxs-lookup"><span data-stu-id="6de04-223">IIS</span></span>

<span data-ttu-id="6de04-224">O IIS é um servidor de arquivos estático com capacidade para Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="6de04-224">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="6de04-225">Para configurar o IIS para hospedar Blazor , consulte [criar um site estático no IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="6de04-225">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="6de04-226">Os ativos publicados são criados na `/bin/Release/{TARGET FRAMEWORK}/publish` pasta.</span><span class="sxs-lookup"><span data-stu-id="6de04-226">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="6de04-227">Hospede o conteúdo da `publish` pasta no servidor Web ou no serviço de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="6de04-227">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="6de04-228">web.config</span><span class="sxs-lookup"><span data-stu-id="6de04-228">web.config</span></span>

<span data-ttu-id="6de04-229">Quando um Blazor projeto é publicado, um `web.config` arquivo é criado com a seguinte configuração do IIS:</span><span class="sxs-lookup"><span data-stu-id="6de04-229">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="6de04-230">Os tipos MIME são definidos para as seguintes extensões de arquivo:</span><span class="sxs-lookup"><span data-stu-id="6de04-230">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="6de04-231">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="6de04-231">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="6de04-232">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="6de04-232">`.json`: `application/json`</span></span>
  * <span data-ttu-id="6de04-233">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="6de04-233">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="6de04-234">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="6de04-234">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="6de04-235">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="6de04-235">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="6de04-236">A compactação HTTP está habilitada para os seguintes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="6de04-236">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="6de04-237">As regras do Módulo de Reescrita de URL são estabelecidas:</span><span class="sxs-lookup"><span data-stu-id="6de04-237">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="6de04-238">Serve o subdiretório onde residem os ativos estáticos do aplicativo ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="6de04-238">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="6de04-239">Crie o roteamento de fallback de SPA para que as solicitações de ativos que não sejam de arquivo sejam redirecionadas para o documento padrão do aplicativo em sua pasta de ativos estáticos ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="6de04-239">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="6de04-240">Usar um web.config personalizado</span><span class="sxs-lookup"><span data-stu-id="6de04-240">Use a custom web.config</span></span>

<span data-ttu-id="6de04-241">Para usar um `web.config` arquivo personalizado, coloque o `web.config` arquivo personalizado na raiz da pasta do projeto e publique o projeto.</span><span class="sxs-lookup"><span data-stu-id="6de04-241">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="6de04-242">Instalação do Módulo de Regeneração de URL</span><span class="sxs-lookup"><span data-stu-id="6de04-242">Install the URL Rewrite Module</span></span>

<span data-ttu-id="6de04-243">O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs.</span><span class="sxs-lookup"><span data-stu-id="6de04-243">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="6de04-244">Por padrão, o módulo não está instalado e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="6de04-244">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="6de04-245">O módulo precisa ser baixado do site do IIS.</span><span class="sxs-lookup"><span data-stu-id="6de04-245">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="6de04-246">Use o Web Platform Installer para instalar o módulo:</span><span class="sxs-lookup"><span data-stu-id="6de04-246">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="6de04-247">Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="6de04-247">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="6de04-248">Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI.</span><span class="sxs-lookup"><span data-stu-id="6de04-248">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="6de04-249">Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.</span><span class="sxs-lookup"><span data-stu-id="6de04-249">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="6de04-250">Copie o instalador para o servidor.</span><span class="sxs-lookup"><span data-stu-id="6de04-250">Copy the installer to the server.</span></span> <span data-ttu-id="6de04-251">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="6de04-251">Run the installer.</span></span> <span data-ttu-id="6de04-252">Selecione o botão **Instalar** e aceite os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="6de04-252">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="6de04-253">Uma reinicialização do servidor não será necessária após a conclusão da instalação.</span><span class="sxs-lookup"><span data-stu-id="6de04-253">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="6de04-254">Configuração do site</span><span class="sxs-lookup"><span data-stu-id="6de04-254">Configure the website</span></span>

<span data-ttu-id="6de04-255">Defina o **Caminho físico** do site como a pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6de04-255">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="6de04-256">A pasta contém:</span><span class="sxs-lookup"><span data-stu-id="6de04-256">The folder contains:</span></span>

* <span data-ttu-id="6de04-257">O `web.config` arquivo que o IIS usa para configurar o site, incluindo as regras de redirecionamento necessárias e os tipos de conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="6de04-257">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="6de04-258">A pasta de ativos estática do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6de04-258">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="6de04-259">Hospedar como um subaplicativo do IIS</span><span class="sxs-lookup"><span data-stu-id="6de04-259">Host as an IIS sub-app</span></span>

<span data-ttu-id="6de04-260">Se um aplicativo autônomo for hospedado como um subaplicativo do IIS, execute um dos seguintes procedimentos:</span><span class="sxs-lookup"><span data-stu-id="6de04-260">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="6de04-261">Desabilite o manipulador do módulo ASP.NET Core herdado.</span><span class="sxs-lookup"><span data-stu-id="6de04-261">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="6de04-262">Remova o manipulador no Blazor arquivo publicado do aplicativo `web.config` adicionando uma `<handlers>` seção ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="6de04-262">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="6de04-263">Desabilite a herança da seção do aplicativo raiz (pai) `<system.webServer>` usando um `<location>` elemento com `inheritInChildApplications` definido como `false` :</span><span class="sxs-lookup"><span data-stu-id="6de04-263">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="6de04-264">A remoção do manipulador ou a desabilitação da herança é executada além da [configuração do caminho base do aplicativo](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="6de04-264">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="6de04-265">Defina o caminho base do aplicativo no arquivo do aplicativo `index.html` como o alias do IIS usado ao configurar o subaplicativo no IIS.</span><span class="sxs-lookup"><span data-stu-id="6de04-265">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="6de04-266">Compactação Brotli e gzip</span><span class="sxs-lookup"><span data-stu-id="6de04-266">Brotli and Gzip compression</span></span>

<span data-ttu-id="6de04-267">O IIS pode ser configurado via `web.config` para servir ativos compactados Brotli ou gzip Blazor .</span><span class="sxs-lookup"><span data-stu-id="6de04-267">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="6de04-268">Para obter um exemplo de configuração, consulte [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="6de04-268">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="6de04-269">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="6de04-269">Troubleshooting</span></span>

<span data-ttu-id="6de04-270">Se um *500 – Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Regeneração de URL está instalado.</span><span class="sxs-lookup"><span data-stu-id="6de04-270">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="6de04-271">Quando o módulo não está instalado, o `web.config` arquivo não pode ser analisado pelo IIS.</span><span class="sxs-lookup"><span data-stu-id="6de04-271">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="6de04-272">Isso impede que o Gerenciador do IIS carregue a configuração do site e o site de serviços de Blazor arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="6de04-272">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="6de04-273">Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="6de04-273">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="6de04-274">Armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="6de04-274">Azure Storage</span></span>

<span data-ttu-id="6de04-275">A hospedagem de arquivos estáticos de [armazenamento do Azure](/azure/storage/) permite Hospedagem de aplicativos sem servidor Blazor .</span><span class="sxs-lookup"><span data-stu-id="6de04-275">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="6de04-276">Nomes de domínio personalizados, CDN (Rede de Distribuição de Conteúdo) do Azure e HTTPS são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="6de04-276">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="6de04-277">Quando o serviço de blob está habilitado para hospedagem de site estático em uma conta de armazenamento:</span><span class="sxs-lookup"><span data-stu-id="6de04-277">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="6de04-278">Defina o **Nome do documento de índice** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="6de04-278">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="6de04-279">Defina o **Caminho do documento de erro** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="6de04-279">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="6de04-280">Razor os componentes e outros pontos de extremidade que não são de arquivo não residem em caminhos físicos no conteúdo estático armazenado pelo serviço BLOB.</span><span class="sxs-lookup"><span data-stu-id="6de04-280">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="6de04-281">Quando uma solicitação para um desses recursos é recebida e o Blazor roteador deve lidar, o erro *404-não encontrado* gerado pelo serviço blob roteia a solicitação para o caminho do **documento de erro**.</span><span class="sxs-lookup"><span data-stu-id="6de04-281">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="6de04-282">O `index.html` blob é retornado e o Blazor roteador carrega e processa o caminho.</span><span class="sxs-lookup"><span data-stu-id="6de04-282">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="6de04-283">Se os arquivos não forem carregados no tempo de execução devido a tipos MIME inadequados nos cabeçalhos dos arquivos `Content-Type` , execute uma das seguintes ações:</span><span class="sxs-lookup"><span data-stu-id="6de04-283">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="6de04-284">Configure suas ferramentas para definir os tipos MIME corretos ( `Content-Type` cabeçalhos) quando os arquivos são implantados.</span><span class="sxs-lookup"><span data-stu-id="6de04-284">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="6de04-285">Altere os tipos MIME ( `Content-Type` cabeçalhos) para os arquivos depois que o aplicativo for implantado.</span><span class="sxs-lookup"><span data-stu-id="6de04-285">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="6de04-286">Em Gerenciador de Armazenamento (portal do Azure) para cada arquivo:</span><span class="sxs-lookup"><span data-stu-id="6de04-286">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="6de04-287">Clique com o botão direito do mouse no arquivo e selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="6de04-287">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="6de04-288">Defina o **ContentType** e selecione o botão **salvar** .</span><span class="sxs-lookup"><span data-stu-id="6de04-288">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="6de04-289">Para mais informações, confira [Hospedagem de site estático no Armazenamento do Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="6de04-289">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="6de04-290">Nginx</span><span class="sxs-lookup"><span data-stu-id="6de04-290">Nginx</span></span>

<span data-ttu-id="6de04-291">O arquivo a seguir `nginx.conf` é simplificado para mostrar como configurar o Nginx para enviar o `index.html` arquivo sempre que ele não encontrar um arquivo correspondente no disco.</span><span class="sxs-lookup"><span data-stu-id="6de04-291">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="6de04-292">Ao definir o [limite de taxa de intermitência Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) com [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) , os Blazor WebAssembly aplicativos podem exigir um grande `burst` valor de parâmetro para acomodar o número relativamente grande de solicitações feitas por um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6de04-292">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="6de04-293">Inicialmente, defina o valor para pelo menos 60:</span><span class="sxs-lookup"><span data-stu-id="6de04-293">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="6de04-294">Aumente o valor se as ferramentas de desenvolvedor do navegador ou uma ferramenta de tráfego de rede indicar que as solicitações estão recebendo um código de status de *503-Serviço indisponível* .</span><span class="sxs-lookup"><span data-stu-id="6de04-294">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="6de04-295">Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).</span><span class="sxs-lookup"><span data-stu-id="6de04-295">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="6de04-296">Nginx no Docker</span><span class="sxs-lookup"><span data-stu-id="6de04-296">Nginx in Docker</span></span>

<span data-ttu-id="6de04-297">Para hospedar Blazor no Docker usando o Nginx, configure o Dockerfile para usar a imagem Nginx baseada em Alpine.</span><span class="sxs-lookup"><span data-stu-id="6de04-297">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="6de04-298">Atualize o Dockerfile para copiar o `nginx.config` arquivo para o contêiner.</span><span class="sxs-lookup"><span data-stu-id="6de04-298">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="6de04-299">Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="6de04-299">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="6de04-300">Apache</span><span class="sxs-lookup"><span data-stu-id="6de04-300">Apache</span></span>

<span data-ttu-id="6de04-301">Para implantar um Blazor WebAssembly aplicativo no CentOS 7 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="6de04-301">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="6de04-302">Crie o arquivo de configuração do Apache.</span><span class="sxs-lookup"><span data-stu-id="6de04-302">Create the Apache configuration file.</span></span> <span data-ttu-id="6de04-303">O exemplo a seguir é um arquivo de configuração simplificado ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="6de04-303">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="6de04-304">Coloque o arquivo de configuração do Apache no `/etc/httpd/conf.d/` diretório, que é o diretório de configuração padrão do Apache no CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="6de04-304">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="6de04-305">Coloque os arquivos do aplicativo no `/var/www/blazorapp` diretório (o local especificado para `DocumentRoot` o no arquivo de configuração).</span><span class="sxs-lookup"><span data-stu-id="6de04-305">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="6de04-306">Reinicie o serviço apache.</span><span class="sxs-lookup"><span data-stu-id="6de04-306">Restart the Apache service.</span></span>

<span data-ttu-id="6de04-307">Para obter mais informações, confira [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="6de04-307">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="6de04-308">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="6de04-308">GitHub Pages</span></span>

<span data-ttu-id="6de04-309">Para tratar as regravações de URL, adicione um `wwwroot/404.html` arquivo com um script que manipula o redirecionamento da solicitação para a `index.html` página.</span><span class="sxs-lookup"><span data-stu-id="6de04-309">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="6de04-310">Para obter um exemplo, consulte o [ Blazor repositório GitHub do SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="6de04-310">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="6de04-311">[Site ativo](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="6de04-311">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="6de04-312">Ao usar um site do projeto em vez de um site da organização, atualize a `<base>` marca no `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="6de04-312">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="6de04-313">Defina o `href` valor do atributo para o nome do repositório GitHub com uma barra à direita (por exemplo, `/my-repository/` ).</span><span class="sxs-lookup"><span data-stu-id="6de04-313">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="6de04-314">No [ Blazor repositório GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), a base `href` é atualizada em publicar pelo arquivo de [ `.github/workflows/main.yml` configuração](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span><span class="sxs-lookup"><span data-stu-id="6de04-314">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="6de04-315">O [ Blazor repositório GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) não é proprietário, mantido ou tem suporte do .net Foundation ou da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="6de04-315">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="6de04-316">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="6de04-316">Host configuration values</span></span>

<span data-ttu-id="6de04-317">os [ Blazor WebAssembly aplicativos](xref:blazor/hosting-models#blazor-webassembly) podem aceitar os seguintes valores de configuração de host como argumentos de linha de comando em tempo de execução no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="6de04-317">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="6de04-318">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="6de04-318">Content root</span></span>

<span data-ttu-id="6de04-319">O `--contentroot` argumento define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo ([raiz de conteúdo](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="6de04-319">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="6de04-320">Nos exemplos a seguir, `/content-root-path` é o caminho raiz do conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6de04-320">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="6de04-321">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="6de04-321">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6de04-322">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="6de04-322">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="6de04-323">Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="6de04-323">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="6de04-324">Esta configuração é usada quando o aplicativo é executado com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="6de04-324">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="6de04-325">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="6de04-325">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6de04-326">Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.</span><span class="sxs-lookup"><span data-stu-id="6de04-326">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="6de04-327">Caminho base</span><span class="sxs-lookup"><span data-stu-id="6de04-327">Path base</span></span>

<span data-ttu-id="6de04-328">O `--pathbase` argumento define o caminho base do aplicativo para um aplicativo executado localmente com um caminho de URL relativo não raiz (a `<base>` marca `href` é definida para um caminho diferente `/` de para preparo e produção).</span><span class="sxs-lookup"><span data-stu-id="6de04-328">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="6de04-329">Nos exemplos a seguir, `/relative-URL-path` é o caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6de04-329">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="6de04-330">Para obter mais informações, consulte [caminho base do aplicativo](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="6de04-330">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6de04-331">Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="6de04-331">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="6de04-332">Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/">` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).</span><span class="sxs-lookup"><span data-stu-id="6de04-332">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="6de04-333">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="6de04-333">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6de04-334">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="6de04-334">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="6de04-335">Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="6de04-335">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="6de04-336">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="6de04-336">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="6de04-337">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="6de04-337">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6de04-338">Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.</span><span class="sxs-lookup"><span data-stu-id="6de04-338">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="6de04-339">URLs</span><span class="sxs-lookup"><span data-stu-id="6de04-339">URLs</span></span>

<span data-ttu-id="6de04-340">O argumento `--urls` define os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.</span><span class="sxs-lookup"><span data-stu-id="6de04-340">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="6de04-341">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="6de04-341">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6de04-342">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="6de04-342">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="6de04-343">Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="6de04-343">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="6de04-344">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="6de04-344">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="6de04-345">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="6de04-345">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6de04-346">Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.</span><span class="sxs-lookup"><span data-stu-id="6de04-346">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="6de04-347">Configurar o cortador</span><span class="sxs-lookup"><span data-stu-id="6de04-347">Configure the Trimmer</span></span>

<span data-ttu-id="6de04-348">Blazor executa a corte de IL (linguagem intermediária) em cada Build de versão para remover o IL desnecessário dos assemblies de saída.</span><span class="sxs-lookup"><span data-stu-id="6de04-348">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="6de04-349">Para obter mais informações, consulte <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="6de04-349">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="6de04-350">Configurar o vinculador</span><span class="sxs-lookup"><span data-stu-id="6de04-350">Configure the Linker</span></span>

<span data-ttu-id="6de04-351">Blazor executa a vinculação de IL (linguagem intermediária) em cada Build de versão para remover o IL desnecessário dos assemblies de saída.</span><span class="sxs-lookup"><span data-stu-id="6de04-351">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="6de04-352">Para obter mais informações, consulte <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="6de04-352">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="6de04-353">Carregamento de recurso de inicialização personalizada</span><span class="sxs-lookup"><span data-stu-id="6de04-353">Custom boot resource loading</span></span>

<span data-ttu-id="6de04-354">Um Blazor WebAssembly aplicativo pode ser inicializado com a `loadBootResource` função para substituir o mecanismo de carregamento do recurso de inicialização interno.</span><span class="sxs-lookup"><span data-stu-id="6de04-354">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="6de04-355">Use `loadBootResource` para os seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="6de04-355">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="6de04-356">Permitir que os usuários carreguem recursos estáticos, como dados de fuso horário ou `dotnet.wasm` de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="6de04-356">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="6de04-357">Carregue assemblies compactados usando uma solicitação HTTP e descompacte-os no cliente para hosts que não dão suporte à busca de conteúdo compactado do servidor.</span><span class="sxs-lookup"><span data-stu-id="6de04-357">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="6de04-358">Recursos de alias para um nome diferente redirecionando cada `fetch` solicitação para um novo nome.</span><span class="sxs-lookup"><span data-stu-id="6de04-358">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="6de04-359">`loadBootResource` os parâmetros aparecem na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="6de04-359">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="6de04-360">Parâmetro</span><span class="sxs-lookup"><span data-stu-id="6de04-360">Parameter</span></span>    | <span data-ttu-id="6de04-361">Descrição</span><span class="sxs-lookup"><span data-stu-id="6de04-361">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="6de04-362">Tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="6de04-362">The type of the resource.</span></span> <span data-ttu-id="6de04-363">Tipos permissiváveis: `assembly` ,, `pdb` , `dotnetjs` `dotnetwasm` , `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="6de04-363">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="6de04-364">O nome do recurso.</span><span class="sxs-lookup"><span data-stu-id="6de04-364">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="6de04-365">O URI relativo ou absoluto do recurso.</span><span class="sxs-lookup"><span data-stu-id="6de04-365">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="6de04-366">A cadeia de caracteres de integridade que representa o conteúdo esperado na resposta.</span><span class="sxs-lookup"><span data-stu-id="6de04-366">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="6de04-367">`loadBootResource` retorna qualquer um dos seguintes para substituir o processo de carregamento:</span><span class="sxs-lookup"><span data-stu-id="6de04-367">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="6de04-368">Cadeia de caracteres de URI.</span><span class="sxs-lookup"><span data-stu-id="6de04-368">URI string.</span></span> <span data-ttu-id="6de04-369">No exemplo a seguir ( `wwwroot/index.html` ), os seguintes arquivos são atendidos de uma CDN em `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="6de04-369">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="6de04-370">Dados de fuso horário</span><span class="sxs-lookup"><span data-stu-id="6de04-370">Timezone data</span></span>

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

* <span data-ttu-id="6de04-371">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="6de04-371">`Promise<Response>`.</span></span> <span data-ttu-id="6de04-372">Passe o `integrity` parâmetro em um cabeçalho para manter o comportamento de verificação de integridade padrão.</span><span class="sxs-lookup"><span data-stu-id="6de04-372">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="6de04-373">O exemplo a seguir ( `wwwroot/index.html` ) adiciona um cabeçalho HTTP personalizado às solicitações de saída e passa o `integrity` parâmetro para a `fetch` chamada:</span><span class="sxs-lookup"><span data-stu-id="6de04-373">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="6de04-374">`null`/`undefined`, o que resulta no comportamento de carregamento padrão.</span><span class="sxs-lookup"><span data-stu-id="6de04-374">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="6de04-375">As fontes externas devem retornar os cabeçalhos CORS necessários para que os navegadores permitam o carregamento de recursos entre origens.</span><span class="sxs-lookup"><span data-stu-id="6de04-375">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="6de04-376">O CDNs geralmente fornece os cabeçalhos necessários por padrão.</span><span class="sxs-lookup"><span data-stu-id="6de04-376">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="6de04-377">Você só precisa especificar tipos para comportamentos personalizados.</span><span class="sxs-lookup"><span data-stu-id="6de04-377">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="6de04-378">Tipos não especificados para `loadBootResource` são carregados pela estrutura de acordo com seus comportamentos de carregamento padrão.</span><span class="sxs-lookup"><span data-stu-id="6de04-378">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="6de04-379">Alterar a extensão do nome de arquivo de arquivos DLL</span><span class="sxs-lookup"><span data-stu-id="6de04-379">Change the filename extension of DLL files</span></span>

<span data-ttu-id="6de04-380">Caso você tenha a necessidade de alterar as extensões de nome de arquivo dos arquivos publicados do aplicativo `.dll` , siga as orientações nesta seção.</span><span class="sxs-lookup"><span data-stu-id="6de04-380">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="6de04-381">Depois de publicar o aplicativo, use um script de shell ou o pipeline de compilação DevOps para renomear `.dll` arquivos para usar uma extensão de arquivo diferente.</span><span class="sxs-lookup"><span data-stu-id="6de04-381">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="6de04-382">Direcione os `.dll` arquivos no `wwwroot` diretório da saída publicada do aplicativo (por exemplo, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="6de04-382">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="6de04-383">Nos exemplos a seguir, `.dll` os arquivos são renomeados para usar a `.bin` extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="6de04-383">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="6de04-384">No Windows:</span><span class="sxs-lookup"><span data-stu-id="6de04-384">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="6de04-385">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6de04-385">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="6de04-386">No Linux ou macOS:</span><span class="sxs-lookup"><span data-stu-id="6de04-386">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="6de04-387">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6de04-387">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="6de04-388">Para usar uma extensão de arquivo diferente da `.bin` , substitua `.bin` os comandos anteriores.</span><span class="sxs-lookup"><span data-stu-id="6de04-388">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="6de04-389">Para resolver os arquivos compactados `blazor.boot.json.gz` `blazor.boot.json.br` , adote uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="6de04-389">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="6de04-390">Remova os arquivos compactados `blazor.boot.json.gz` `blazor.boot.json.br` .</span><span class="sxs-lookup"><span data-stu-id="6de04-390">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="6de04-391">A compactação está desabilitada com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="6de04-391">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="6de04-392">Recompacte o `blazor.boot.json` arquivo atualizado.</span><span class="sxs-lookup"><span data-stu-id="6de04-392">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="6de04-393">As diretrizes anteriores também se aplicam quando os ativos de trabalho do serviço estão em uso.</span><span class="sxs-lookup"><span data-stu-id="6de04-393">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="6de04-394">Remova ou recompacte `wwwroot/service-worker-assets.js.br` e `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="6de04-394">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="6de04-395">Caso contrário, as verificações de integridade de arquivo falharão no navegador.</span><span class="sxs-lookup"><span data-stu-id="6de04-395">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="6de04-396">O exemplo de Windows a seguir usa um script do PowerShell colocado na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="6de04-396">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="6de04-397">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="6de04-397">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="6de04-398">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6de04-398">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="6de04-399">No arquivo de projeto, o script é executado após a publicação do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="6de04-399">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="6de04-400">Ao renomear e carregar lentos os mesmos assemblies, consulte as diretrizes em <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .</span><span class="sxs-lookup"><span data-stu-id="6de04-400">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>
