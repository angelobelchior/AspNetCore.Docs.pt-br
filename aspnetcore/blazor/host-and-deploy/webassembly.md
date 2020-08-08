---
title: Hospedar e implantar ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Saiba como hospedar e implantar um Blazor aplicativo usando ASP.NET Core, CDN (redes de distribuição de conteúdo), servidores de arquivos e páginas do github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
no-loc:
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
ms.openlocfilehash: 06059e0f9ff6a3f4073d8d01d1ac541c30ad1ab1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014185"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="7ba28-103">Hospedar e implantar ASP.NET CoreBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7ba28-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="7ba28-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams)e [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="7ba28-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="7ba28-105">Com o [ Blazor WebAssembly modelo de hospedagem](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="7ba28-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="7ba28-106">O Blazor aplicativo, suas dependências e o tempo de execução do .NET são baixados para o navegador em paralelo.</span><span class="sxs-lookup"><span data-stu-id="7ba28-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="7ba28-107">O aplicativo é executado diretamente no thread da interface do usuário do navegador.</span><span class="sxs-lookup"><span data-stu-id="7ba28-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="7ba28-108">Há suporte para as seguintes estratégias de implantação:</span><span class="sxs-lookup"><span data-stu-id="7ba28-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="7ba28-109">O Blazor aplicativo é servido por um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ba28-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="7ba28-110">Esta estratégia é abordada na seção [Implantação hospedada com o ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="7ba28-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="7ba28-111">O Blazor aplicativo é colocado em um servidor Web ou serviço de hospedagem estática, em que o .net não é usado para servir o Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba28-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="7ba28-112">Essa estratégia é abordada na seção [implantação autônoma](#standalone-deployment) , que inclui informações sobre como hospedar um Blazor WebAssembly aplicativo como um subaplicativo do IIS.</span><span class="sxs-lookup"><span data-stu-id="7ba28-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="7ba28-113">Compactação</span><span class="sxs-lookup"><span data-stu-id="7ba28-113">Compression</span></span>

<span data-ttu-id="7ba28-114">Quando um Blazor WebAssembly aplicativo é publicado, a saída é compactada estaticamente durante a publicação para reduzir o tamanho do aplicativo e remover a sobrecarga de compactação de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="7ba28-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="7ba28-115">Os seguintes algoritmos de compactação são usados:</span><span class="sxs-lookup"><span data-stu-id="7ba28-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="7ba28-116">[Brotli](https://tools.ietf.org/html/rfc7932) (nível mais alto)</span><span class="sxs-lookup"><span data-stu-id="7ba28-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="7ba28-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="7ba28-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="7ba28-118">Blazoro se baseia no host para o fornecer os arquivos compactados apropriados.</span><span class="sxs-lookup"><span data-stu-id="7ba28-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="7ba28-119">Ao usar um projeto ASP.NET Core hospedado, o projeto host é capaz de executar a negociação de conteúdo e fornecer os arquivos compactados estaticamente.</span><span class="sxs-lookup"><span data-stu-id="7ba28-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="7ba28-120">Ao hospedar um Blazor WebAssembly aplicativo autônomo, um trabalho adicional pode ser necessário para garantir que arquivos compactados estaticamente sejam atendidos:</span><span class="sxs-lookup"><span data-stu-id="7ba28-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="7ba28-121">Para `web.config` a configuração de compactação do IIS, consulte a seção [IIS: Brotli e a compactação Gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="7ba28-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="7ba28-122">Ao hospedar soluções de hospedagem estática que não dão suporte à negociação de conteúdo de arquivo compactado estaticamente, como páginas do GitHub, considere configurar o aplicativo para buscar e decodificar arquivos compactados Brotli:</span><span class="sxs-lookup"><span data-stu-id="7ba28-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="7ba28-123">Obtenha o decodificador Brotli do JavaScript do [repositório GitHub do Google/Brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="7ba28-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="7ba28-124">A partir de julho de 2020, o arquivo de decodificador é nomeado `decode.min.js` e encontrado na [ `js` pasta](https://github.com/google/brotli/tree/master/js)do repositório.</span><span class="sxs-lookup"><span data-stu-id="7ba28-124">As of July 2020, the decoder file is named `decode.min.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  * <span data-ttu-id="7ba28-125">Atualize o aplicativo para usar o decodificador.</span><span class="sxs-lookup"><span data-stu-id="7ba28-125">Update the app to use the decoder.</span></span> <span data-ttu-id="7ba28-126">Altere a marcação dentro da marca de fechamento `<body>` `wwwroot/index.html` para o seguinte:</span><span class="sxs-lookup"><span data-stu-id="7ba28-126">Change the markup inside the the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
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
 
<span data-ttu-id="7ba28-127">Para desabilitar a compactação, adicione a `BlazorEnableCompression` Propriedade MSBuild ao arquivo de projeto do aplicativo e defina o valor como `false` :</span><span class="sxs-lookup"><span data-stu-id="7ba28-127">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="7ba28-128">Reescrever as URLs para obter o roteamento correto</span><span class="sxs-lookup"><span data-stu-id="7ba28-128">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="7ba28-129">O roteamento de solicitações para componentes de página em um Blazor WebAssembly aplicativo não é tão simples quanto o roteamento de solicitações em um Blazor Server aplicativo hospedado.</span><span class="sxs-lookup"><span data-stu-id="7ba28-129">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="7ba28-130">Considere um Blazor WebAssembly aplicativo com dois componentes:</span><span class="sxs-lookup"><span data-stu-id="7ba28-130">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="7ba28-131">`Main.razor`: Carrega na raiz do aplicativo e contém um link para o `About` componente ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="7ba28-131">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="7ba28-132">`About.razor`: `About` componente.</span><span class="sxs-lookup"><span data-stu-id="7ba28-132">`About.razor`: `About` component.</span></span>

<span data-ttu-id="7ba28-133">Quando o documento padrão do aplicativo é solicitado usando a barra de endereços do navegador (por exemplo, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="7ba28-133">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="7ba28-134">O navegador faz uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="7ba28-134">The browser makes a request.</span></span>
1. <span data-ttu-id="7ba28-135">A página padrão é retornada, o que geralmente é `index.html` .</span><span class="sxs-lookup"><span data-stu-id="7ba28-135">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="7ba28-136">`index.html`Inicializa o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba28-136">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="7ba28-137">Blazoro roteador do é carregado e o Razor `Main` componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="7ba28-137">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="7ba28-138">Na página principal, selecionar o link para o `About` componente funciona no cliente, pois o Blazor roteador interrompe o navegador de fazer uma solicitação na Internet para `www.contoso.com` `About` e serve o componente renderizado em `About` si.</span><span class="sxs-lookup"><span data-stu-id="7ba28-138">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="7ba28-139">Todas as solicitações de pontos de extremidade internos *no Blazor WebAssembly aplicativo* funcionam da mesma maneira: as solicitações não disparam solicitações baseadas em navegador para recursos hospedados no servidor na Internet.</span><span class="sxs-lookup"><span data-stu-id="7ba28-139">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="7ba28-140">O roteador trata das solicitações internamente.</span><span class="sxs-lookup"><span data-stu-id="7ba28-140">The router handles the requests internally.</span></span>

<span data-ttu-id="7ba28-141">Se uma solicitação for feita usando a barra de endereços do navegador para `www.contoso.com/About`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="7ba28-141">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="7ba28-142">Este recurso não existe no host do aplicativo na Internet; portanto, uma resposta *404 – Não Encontrado* é retornada.</span><span class="sxs-lookup"><span data-stu-id="7ba28-142">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="7ba28-143">Como os navegadores fazem solicitações para hosts baseados na Internet para páginas do lado do cliente, servidores Web e serviços de hospedagem devem regravar todas as solicitações de recursos não fisicamente no servidor para a `index.html` página.</span><span class="sxs-lookup"><span data-stu-id="7ba28-143">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="7ba28-144">Quando `index.html` é retornado, o roteador do aplicativo Blazor assume o failover e responde com o recurso correto.</span><span class="sxs-lookup"><span data-stu-id="7ba28-144">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="7ba28-145">Ao implantar em um servidor IIS, você pode usar o módulo de reescrita de URL com o arquivo publicado do aplicativo `web.config` .</span><span class="sxs-lookup"><span data-stu-id="7ba28-145">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="7ba28-146">Para obter mais informações, consulte a seção [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="7ba28-146">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="7ba28-147">Implantação hospedada com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ba28-147">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="7ba28-148">Uma *implantação hospedada* serve o Blazor WebAssembly aplicativo para os navegadores de um [aplicativo ASP.NET Core](xref:index) executado em um servidor Web.</span><span class="sxs-lookup"><span data-stu-id="7ba28-148">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="7ba28-149">O Blazor WebAssembly aplicativo cliente é publicado na `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` pasta do aplicativo de servidor, juntamente com quaisquer outros ativos da Web estáticos do aplicativo de servidor.</span><span class="sxs-lookup"><span data-stu-id="7ba28-149">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="7ba28-150">Os dois aplicativos são implantados juntos.</span><span class="sxs-lookup"><span data-stu-id="7ba28-150">The two apps are deployed together.</span></span> <span data-ttu-id="7ba28-151">É necessário um servidor Web capaz de hospedar um aplicativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ba28-151">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="7ba28-152">Para uma implantação hospedada, o Visual Studio inclui o modelo de projeto de \*\* Blazor WebAssembly aplicativo\*\* ( `blazorwasm` modelo ao usar o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando) com a **`Hosted`** opção selecionada ( `-ho|--hosted` ao usar o `dotnet new` comando).</span><span class="sxs-lookup"><span data-stu-id="7ba28-152">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="7ba28-153">Para obter mais informações sobre a implantação e a hospedagem de aplicativo do ASP.NET Core, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="7ba28-153">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="7ba28-154">Confira como implantar o Serviço de Aplicativo do Azure em <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="7ba28-154">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="7ba28-155">Implantação hospedada com vários Blazor WebAssembly aplicativos</span><span class="sxs-lookup"><span data-stu-id="7ba28-155">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="7ba28-156">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="7ba28-156">App configuration</span></span>

<span data-ttu-id="7ba28-157">Para configurar uma solução hospedada Blazor para atender a vários Blazor WebAssembly aplicativos:</span><span class="sxs-lookup"><span data-stu-id="7ba28-157">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="7ba28-158">Use uma solução hospedada existente Blazor ou crie uma nova solução a partir do Blazor modelo de projeto hospedado.</span><span class="sxs-lookup"><span data-stu-id="7ba28-158">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="7ba28-159">No arquivo de projeto do aplicativo cliente, adicione uma `<StaticWebAssetBasePath>` propriedade ao `<PropertyGroup>` com um valor de `FirstApp` para definir o caminho base para os ativos estáticos do projeto:</span><span class="sxs-lookup"><span data-stu-id="7ba28-159">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="7ba28-160">Adicione um segundo aplicativo cliente à solução:</span><span class="sxs-lookup"><span data-stu-id="7ba28-160">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="7ba28-161">Adicione uma pasta chamada `SecondClient` à pasta da solução.</span><span class="sxs-lookup"><span data-stu-id="7ba28-161">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="7ba28-162">Crie um Blazor WebAssembly aplicativo chamado `SecondBlazorApp.Client` na `SecondClient` pasta do modelo de Blazor WebAssembly projeto.</span><span class="sxs-lookup"><span data-stu-id="7ba28-162">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="7ba28-163">No arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7ba28-163">In the app's project file:</span></span>

    * <span data-ttu-id="7ba28-164">Adicione uma `<StaticWebAssetBasePath>` propriedade ao `<PropertyGroup>` com um valor de `SecondApp` :</span><span class="sxs-lookup"><span data-stu-id="7ba28-164">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="7ba28-165">Adicione uma referência de projeto ao `Shared` projeto:</span><span class="sxs-lookup"><span data-stu-id="7ba28-165">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="7ba28-166">O espaço reservado `{SOLUTION NAME}` é o nome da solução.</span><span class="sxs-lookup"><span data-stu-id="7ba28-166">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="7ba28-167">No arquivo de projeto do aplicativo do servidor, crie uma referência de projeto para o aplicativo cliente adicionado:</span><span class="sxs-lookup"><span data-stu-id="7ba28-167">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="7ba28-168">No arquivo do aplicativo do servidor `Properties/launchSettings.json` , configure o `applicationUrl` do perfil Kestrel ( `{SOLUTION NAME}.Server` ) para acessar os aplicativos cliente nas portas 5001 e 5002:</span><span class="sxs-lookup"><span data-stu-id="7ba28-168">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="7ba28-169">No método do aplicativo do servidor `Startup.Configure` ( `Startup.cs` ), remova as seguintes linhas, que aparecem após a chamada para <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :</span><span class="sxs-lookup"><span data-stu-id="7ba28-169">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

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

  <span data-ttu-id="7ba28-170">Adicione middleware que mapeia solicitações para os aplicativos cliente.</span><span class="sxs-lookup"><span data-stu-id="7ba28-170">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="7ba28-171">O exemplo a seguir configura o middleware para ser executado quando:</span><span class="sxs-lookup"><span data-stu-id="7ba28-171">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="7ba28-172">A porta de solicitação é 5001 para o aplicativo cliente original ou 5002 para o aplicativo cliente adicionado.</span><span class="sxs-lookup"><span data-stu-id="7ba28-172">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="7ba28-173">O host de solicitação é `firstapp.com` para o aplicativo cliente original ou `secondapp.com` para o aplicativo cliente adicionado.</span><span class="sxs-lookup"><span data-stu-id="7ba28-173">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="7ba28-174">O exemplo mostrado nesta seção requer configuração adicional para o:</span><span class="sxs-lookup"><span data-stu-id="7ba28-174">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="7ba28-175">Acessar os aplicativos em domínios de host de exemplo `firstapp.com` e `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="7ba28-175">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="7ba28-176">Certificados para os aplicativos cliente habilitarem a segurança TLS (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7ba28-176">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="7ba28-177">A configuração necessária está além do escopo deste artigo e depende de como a solução está hospedada.</span><span class="sxs-lookup"><span data-stu-id="7ba28-177">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="7ba28-178">Para obter mais informações, consulte os [artigos sobre host e implantar](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="7ba28-178">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="7ba28-179">Coloque o código a seguir onde as linhas foram removidas anteriormente:</span><span class="sxs-lookup"><span data-stu-id="7ba28-179">Place the following code where the lines were removed earlier:</span></span>

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

* <span data-ttu-id="7ba28-180">No controlador de previsão do tempo do aplicativo do servidor ( `Controllers/WeatherForecastController.cs` ), substitua a rota existente ( `[Route("[controller]")]` ) `WeatherForecastController` por pelas seguintes rotas:</span><span class="sxs-lookup"><span data-stu-id="7ba28-180">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="7ba28-181">O middleware adicionado ao método do aplicativo do servidor `Startup.Configure` anteriormente modifica as solicitações de entrada para `/WeatherForecast` `/FirstApp/WeatherForecast` ou `/SecondApp/WeatherForecast` dependendo da porta (5001/5002) ou do domínio ( `firstapp.com` / `secondapp.com` ).</span><span class="sxs-lookup"><span data-stu-id="7ba28-181">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="7ba28-182">As rotas de controlador anteriores são necessárias para retornar dados meteorológicos do aplicativo de servidor para os aplicativos cliente.</span><span class="sxs-lookup"><span data-stu-id="7ba28-182">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="7ba28-183">Ativos estáticos e bibliotecas de classes</span><span class="sxs-lookup"><span data-stu-id="7ba28-183">Static assets and class libraries</span></span>

<span data-ttu-id="7ba28-184">Use as seguintes abordagens para ativos estáticos:</span><span class="sxs-lookup"><span data-stu-id="7ba28-184">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="7ba28-185">Quando o ativo estiver na pasta do aplicativo cliente `wwwroot` , forneça seus caminhos normalmente:</span><span class="sxs-lookup"><span data-stu-id="7ba28-185">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="7ba28-186">Quando o ativo estiver na `wwwroot` pasta de uma [ Razor biblioteca de classes (RCL)](xref:blazor/components/class-libraries), referencie o ativo estático no aplicativo cliente de acordo com as diretrizes no [artigo RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="7ba28-186">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7ba28-187">Os componentes fornecidos a um aplicativo cliente por uma biblioteca de classes são referenciados normalmente.</span><span class="sxs-lookup"><span data-stu-id="7ba28-187">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="7ba28-188">Se algum componente exigir folhas de estilo ou arquivos JavaScript, use uma das abordagens a seguir para obter os ativos estáticos:</span><span class="sxs-lookup"><span data-stu-id="7ba28-188">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="7ba28-189">O arquivo do aplicativo cliente `wwwroot/index.html` pode vincular ( `<link>` ) aos ativos estáticos.</span><span class="sxs-lookup"><span data-stu-id="7ba28-189">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="7ba28-190">O componente pode usar o [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) da estrutura para obter os ativos estáticos.</span><span class="sxs-lookup"><span data-stu-id="7ba28-190">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="7ba28-191">As abordagens anteriores são demonstradas nos exemplos a seguir.</span><span class="sxs-lookup"><span data-stu-id="7ba28-191">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="7ba28-192">Os componentes fornecidos a um aplicativo cliente por uma biblioteca de classes são referenciados normalmente.</span><span class="sxs-lookup"><span data-stu-id="7ba28-192">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="7ba28-193">Se algum componente exigir folhas de estilo ou arquivos JavaScript, o arquivo do aplicativo cliente `wwwroot/index.html` deverá incluir os links de ativo estático corretos.</span><span class="sxs-lookup"><span data-stu-id="7ba28-193">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="7ba28-194">Essas abordagens são demonstradas nos exemplos a seguir.</span><span class="sxs-lookup"><span data-stu-id="7ba28-194">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="7ba28-195">Adicione o componente a seguir `Jeep` a um dos aplicativos cliente.</span><span class="sxs-lookup"><span data-stu-id="7ba28-195">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="7ba28-196">O `Jeep` componente usa:</span><span class="sxs-lookup"><span data-stu-id="7ba28-196">The `Jeep` component uses:</span></span>

* <span data-ttu-id="7ba28-197">Uma imagem da pasta do aplicativo cliente `wwwroot` ( `jeep-cj.png` ).</span><span class="sxs-lookup"><span data-stu-id="7ba28-197">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="7ba28-198">Uma imagem de uma pasta de [ Razor biblioteca de componentes](xref:blazor/components/class-libraries) () adicionada () `JeepImage` `wwwroot` `jeep-yj.png` .</span><span class="sxs-lookup"><span data-stu-id="7ba28-198">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="7ba28-199">O componente de exemplo ( `Component1` ) é criado automaticamente pelo modelo de projeto RCL quando a `JeepImage` biblioteca é adicionada à solução.</span><span class="sxs-lookup"><span data-stu-id="7ba28-199">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

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
> <span data-ttu-id="7ba28-200">Não **publique imagens** de veículos publicamente, a menos que você tenha as imagens.</span><span class="sxs-lookup"><span data-stu-id="7ba28-200">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="7ba28-201">Caso contrário, você arriscará a violação de direitos autorais.</span><span class="sxs-lookup"><span data-stu-id="7ba28-201">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7ba28-202">A imagem da biblioteca `jeep-yj.png` também pode ser adicionada ao componente da biblioteca `Component1` ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="7ba28-202">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="7ba28-203">Para fornecer a `my-component` classe CSS à página do aplicativo cliente, vincule à folha de estilos da biblioteca usando o [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)da estrutura:</span><span class="sxs-lookup"><span data-stu-id="7ba28-203">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

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

<span data-ttu-id="7ba28-204">Uma alternativa ao uso do [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) é carregar a folha de estilos do arquivo do aplicativo cliente `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="7ba28-204">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="7ba28-205">Essa abordagem torna a folha de estilos disponível para todos os componentes no aplicativo cliente:</span><span class="sxs-lookup"><span data-stu-id="7ba28-205">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="7ba28-206">A imagem da biblioteca `jeep-yj.png` também pode ser adicionada ao componente da biblioteca `Component1` ( `Component1.razor` ):</span><span class="sxs-lookup"><span data-stu-id="7ba28-206">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

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

<span data-ttu-id="7ba28-207">O arquivo do aplicativo cliente `wwwroot/index.html` solicita a folha de estilos da biblioteca com a seguinte `<link>` marca adicionada:</span><span class="sxs-lookup"><span data-stu-id="7ba28-207">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="7ba28-208">Adicione navegação ao `Jeep` componente no componente do aplicativo cliente `NavMenu` ( `Shared/NavMenu.razor` ):</span><span class="sxs-lookup"><span data-stu-id="7ba28-208">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="7ba28-209">Para obter mais informações sobre o RCLs, consulte:</span><span class="sxs-lookup"><span data-stu-id="7ba28-209">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="7ba28-210">Implantação autônoma</span><span class="sxs-lookup"><span data-stu-id="7ba28-210">Standalone deployment</span></span>

<span data-ttu-id="7ba28-211">Uma *implantação autônoma* serve o Blazor WebAssembly aplicativo como um conjunto de arquivos estáticos que são solicitados diretamente pelos clientes.</span><span class="sxs-lookup"><span data-stu-id="7ba28-211">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="7ba28-212">Qualquer servidor de arquivos estático é capaz de atender ao Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba28-212">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="7ba28-213">Os ativos de implantação autônomo são publicados na `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` pasta.</span><span class="sxs-lookup"><span data-stu-id="7ba28-213">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="7ba28-214">Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="7ba28-214">Azure App Service</span></span>

<span data-ttu-id="7ba28-215">Blazor WebAssemblyos aplicativos podem ser implantados em serviços Azure App no Windows, que hospedam o aplicativo no [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="7ba28-215">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="7ba28-216">Blazor WebAssemblyAtualmente, não há suporte para a implantação de um aplicativo autônomo no serviço Azure app para Linux.</span><span class="sxs-lookup"><span data-stu-id="7ba28-216">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="7ba28-217">Uma imagem do servidor Linux para hospedar o aplicativo não está disponível no momento.</span><span class="sxs-lookup"><span data-stu-id="7ba28-217">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="7ba28-218">O trabalho está em andamento para habilitar esse cenário.</span><span class="sxs-lookup"><span data-stu-id="7ba28-218">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="7ba28-219">IIS</span><span class="sxs-lookup"><span data-stu-id="7ba28-219">IIS</span></span>

<span data-ttu-id="7ba28-220">O IIS é um servidor de arquivos estático com capacidade para Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="7ba28-220">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="7ba28-221">Para configurar o IIS para hospedar Blazor , consulte [criar um site estático no IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="7ba28-221">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="7ba28-222">Os ativos publicados são criados na `/bin/Release/{TARGET FRAMEWORK}/publish` pasta.</span><span class="sxs-lookup"><span data-stu-id="7ba28-222">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="7ba28-223">Hospede o conteúdo da `publish` pasta no servidor Web ou no serviço de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="7ba28-223">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="7ba28-224">web.config</span><span class="sxs-lookup"><span data-stu-id="7ba28-224">web.config</span></span>

<span data-ttu-id="7ba28-225">Quando um Blazor projeto é publicado, um `web.config` arquivo é criado com a seguinte configuração do IIS:</span><span class="sxs-lookup"><span data-stu-id="7ba28-225">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="7ba28-226">Os tipos MIME são definidos para as seguintes extensões de arquivo:</span><span class="sxs-lookup"><span data-stu-id="7ba28-226">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="7ba28-227">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="7ba28-227">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="7ba28-228">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="7ba28-228">`.json`: `application/json`</span></span>
  * <span data-ttu-id="7ba28-229">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="7ba28-229">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="7ba28-230">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="7ba28-230">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="7ba28-231">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="7ba28-231">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="7ba28-232">A compactação HTTP está habilitada para os seguintes tipos MIME:</span><span class="sxs-lookup"><span data-stu-id="7ba28-232">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="7ba28-233">As regras do Módulo de Reescrita de URL são estabelecidas:</span><span class="sxs-lookup"><span data-stu-id="7ba28-233">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="7ba28-234">Serve o subdiretório onde residem os ativos estáticos do aplicativo ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="7ba28-234">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="7ba28-235">Crie o roteamento de fallback de SPA para que as solicitações de ativos que não sejam de arquivo sejam redirecionadas para o documento padrão do aplicativo em sua pasta de ativos estáticos ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="7ba28-235">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="7ba28-236">Usar um web.config personalizado</span><span class="sxs-lookup"><span data-stu-id="7ba28-236">Use a custom web.config</span></span>

<span data-ttu-id="7ba28-237">Para usar um `web.config` arquivo personalizado, coloque o `web.config` arquivo personalizado na raiz da pasta do projeto e publique o projeto.</span><span class="sxs-lookup"><span data-stu-id="7ba28-237">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="7ba28-238">Instalação do Módulo de Regeneração de URL</span><span class="sxs-lookup"><span data-stu-id="7ba28-238">Install the URL Rewrite Module</span></span>

<span data-ttu-id="7ba28-239">O [Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite) é necessário para reescrever URLs.</span><span class="sxs-lookup"><span data-stu-id="7ba28-239">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="7ba28-240">Por padrão, o módulo não está instalado e não está disponível para instalação como um recurso do serviço de função do servidor Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="7ba28-240">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="7ba28-241">O módulo precisa ser baixado do site do IIS.</span><span class="sxs-lookup"><span data-stu-id="7ba28-241">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="7ba28-242">Use o Web Platform Installer para instalar o módulo:</span><span class="sxs-lookup"><span data-stu-id="7ba28-242">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="7ba28-243">Localmente, navegue até a [página de downloads do Módulo de Reescrita de URL](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="7ba28-243">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="7ba28-244">Para obter a versão em inglês, selecione **WebPI** para baixar o instalador do WebPI.</span><span class="sxs-lookup"><span data-stu-id="7ba28-244">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="7ba28-245">Para outros idiomas, selecione a arquitetura apropriada para o servidor (x86/x64) para baixar o instalador.</span><span class="sxs-lookup"><span data-stu-id="7ba28-245">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="7ba28-246">Copie o instalador para o servidor.</span><span class="sxs-lookup"><span data-stu-id="7ba28-246">Copy the installer to the server.</span></span> <span data-ttu-id="7ba28-247">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="7ba28-247">Run the installer.</span></span> <span data-ttu-id="7ba28-248">Selecione o botão **Instalar** e aceite os termos de licença.</span><span class="sxs-lookup"><span data-stu-id="7ba28-248">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="7ba28-249">Uma reinicialização do servidor não será necessária após a conclusão da instalação.</span><span class="sxs-lookup"><span data-stu-id="7ba28-249">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="7ba28-250">Configuração do site</span><span class="sxs-lookup"><span data-stu-id="7ba28-250">Configure the website</span></span>

<span data-ttu-id="7ba28-251">Defina o **Caminho físico** do site como a pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba28-251">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="7ba28-252">A pasta contém:</span><span class="sxs-lookup"><span data-stu-id="7ba28-252">The folder contains:</span></span>

* <span data-ttu-id="7ba28-253">O `web.config` arquivo que o IIS usa para configurar o site, incluindo as regras de redirecionamento necessárias e os tipos de conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba28-253">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="7ba28-254">A pasta de ativos estática do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba28-254">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="7ba28-255">Hospedar como um subaplicativo do IIS</span><span class="sxs-lookup"><span data-stu-id="7ba28-255">Host as an IIS sub-app</span></span>

<span data-ttu-id="7ba28-256">Se um aplicativo autônomo for hospedado como um subaplicativo do IIS, execute um dos seguintes procedimentos:</span><span class="sxs-lookup"><span data-stu-id="7ba28-256">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="7ba28-257">Desabilite o manipulador do módulo ASP.NET Core herdado.</span><span class="sxs-lookup"><span data-stu-id="7ba28-257">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="7ba28-258">Remova o manipulador no Blazor arquivo publicado do aplicativo `web.config` adicionando uma `<handlers>` seção ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="7ba28-258">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="7ba28-259">Desabilite a herança da seção do aplicativo raiz (pai) `<system.webServer>` usando um `<location>` elemento com `inheritInChildApplications` definido como `false` :</span><span class="sxs-lookup"><span data-stu-id="7ba28-259">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="7ba28-260">A remoção do manipulador ou a desabilitação da herança é executada além da [configuração do caminho base do aplicativo](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="7ba28-260">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="7ba28-261">Defina o caminho base do aplicativo no arquivo do aplicativo `index.html` como o alias do IIS usado ao configurar o subaplicativo no IIS.</span><span class="sxs-lookup"><span data-stu-id="7ba28-261">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="7ba28-262">Compactação Brotli e gzip</span><span class="sxs-lookup"><span data-stu-id="7ba28-262">Brotli and Gzip compression</span></span>

<span data-ttu-id="7ba28-263">O IIS pode ser configurado via `web.config` para servir ativos compactados Brotli ou gzip Blazor .</span><span class="sxs-lookup"><span data-stu-id="7ba28-263">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="7ba28-264">Para obter um exemplo de configuração, consulte [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="7ba28-264">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="7ba28-265">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="7ba28-265">Troubleshooting</span></span>

<span data-ttu-id="7ba28-266">Se um *500 – Erro Interno do Servidor* for recebido e o Gerenciador do IIS gerar erros ao tentar acessar a configuração do site, confirme se o Módulo de Regeneração de URL está instalado.</span><span class="sxs-lookup"><span data-stu-id="7ba28-266">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="7ba28-267">Quando o módulo não está instalado, o `web.config` arquivo não pode ser analisado pelo IIS.</span><span class="sxs-lookup"><span data-stu-id="7ba28-267">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="7ba28-268">Isso impede que o Gerenciador do IIS carregue a configuração do site e o site de serviços de Blazor arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="7ba28-268">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="7ba28-269">Para obter mais informações de como solucionar problemas de implantações no IIS, confira <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="7ba28-269">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="7ba28-270">Armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="7ba28-270">Azure Storage</span></span>

<span data-ttu-id="7ba28-271">A hospedagem de arquivos estáticos de [armazenamento do Azure](/azure/storage/) permite Hospedagem de aplicativos sem servidor Blazor .</span><span class="sxs-lookup"><span data-stu-id="7ba28-271">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="7ba28-272">Nomes de domínio personalizados, CDN (Rede de Distribuição de Conteúdo) do Azure e HTTPS são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="7ba28-272">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="7ba28-273">Quando o serviço de blob está habilitado para hospedagem de site estático em uma conta de armazenamento:</span><span class="sxs-lookup"><span data-stu-id="7ba28-273">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="7ba28-274">Defina o **Nome do documento de índice** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="7ba28-274">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="7ba28-275">Defina o **Caminho do documento de erro** como `index.html`.</span><span class="sxs-lookup"><span data-stu-id="7ba28-275">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="7ba28-276">Razoros componentes e outros pontos de extremidade que não são de arquivo não residem em caminhos físicos no conteúdo estático armazenado pelo serviço BLOB.</span><span class="sxs-lookup"><span data-stu-id="7ba28-276">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="7ba28-277">Quando uma solicitação para um desses recursos é recebida e o Blazor roteador deve lidar, o erro *404-não encontrado* gerado pelo serviço blob roteia a solicitação para o caminho do **documento de erro**.</span><span class="sxs-lookup"><span data-stu-id="7ba28-277">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="7ba28-278">O `index.html` blob é retornado e o Blazor roteador carrega e processa o caminho.</span><span class="sxs-lookup"><span data-stu-id="7ba28-278">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="7ba28-279">Se os arquivos não forem carregados no tempo de execução devido a tipos MIME inadequados nos cabeçalhos dos arquivos `Content-Type` , execute uma das seguintes ações:</span><span class="sxs-lookup"><span data-stu-id="7ba28-279">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="7ba28-280">Configure suas ferramentas para definir os tipos MIME corretos ( `Content-Type` cabeçalhos) quando os arquivos são implantados.</span><span class="sxs-lookup"><span data-stu-id="7ba28-280">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="7ba28-281">Altere os tipos MIME ( `Content-Type` cabeçalhos) para os arquivos depois que o aplicativo for implantado.</span><span class="sxs-lookup"><span data-stu-id="7ba28-281">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="7ba28-282">Em Gerenciador de Armazenamento (portal do Azure) para cada arquivo:</span><span class="sxs-lookup"><span data-stu-id="7ba28-282">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="7ba28-283">Clique com o botão direito do mouse no arquivo e selecione **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="7ba28-283">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="7ba28-284">Defina o **ContentType** e selecione o botão **salvar** .</span><span class="sxs-lookup"><span data-stu-id="7ba28-284">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="7ba28-285">Para mais informações, confira [Hospedagem de site estático no Armazenamento do Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="7ba28-285">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="7ba28-286">Nginx</span><span class="sxs-lookup"><span data-stu-id="7ba28-286">Nginx</span></span>

<span data-ttu-id="7ba28-287">O arquivo a seguir `nginx.conf` é simplificado para mostrar como configurar o Nginx para enviar o `index.html` arquivo sempre que ele não encontrar um arquivo correspondente no disco.</span><span class="sxs-lookup"><span data-stu-id="7ba28-287">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="7ba28-288">Ao definir o [limite de taxa de intermitência Nginx](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) com [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) , os Blazor WebAssembly aplicativos podem exigir um grande `burst` valor de parâmetro para acomodar o número relativamente grande de solicitações feitas por um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba28-288">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="7ba28-289">Inicialmente, defina o valor para pelo menos 60:</span><span class="sxs-lookup"><span data-stu-id="7ba28-289">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="7ba28-290">Aumente o valor se as ferramentas de desenvolvedor do navegador ou uma ferramenta de tráfego de rede indicar que as solicitações estão recebendo um código de status de *503-Serviço indisponível* .</span><span class="sxs-lookup"><span data-stu-id="7ba28-290">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="7ba28-291">Para obter mais informações sobre a configuração do servidor Web Nginx de produção, confira [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Criando arquivos de configuração do NGINX Plus e do NGINX).</span><span class="sxs-lookup"><span data-stu-id="7ba28-291">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="7ba28-292">Nginx no Docker</span><span class="sxs-lookup"><span data-stu-id="7ba28-292">Nginx in Docker</span></span>

<span data-ttu-id="7ba28-293">Para hospedar Blazor no Docker usando o Nginx, configure o Dockerfile para usar a imagem Nginx baseada em Alpine.</span><span class="sxs-lookup"><span data-stu-id="7ba28-293">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="7ba28-294">Atualize o Dockerfile para copiar o `nginx.config` arquivo para o contêiner.</span><span class="sxs-lookup"><span data-stu-id="7ba28-294">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="7ba28-295">Adicione uma linha ao Dockerfile, conforme é mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="7ba28-295">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="7ba28-296">Apache</span><span class="sxs-lookup"><span data-stu-id="7ba28-296">Apache</span></span>

<span data-ttu-id="7ba28-297">Para implantar um Blazor WebAssembly aplicativo no CentOS 7 ou posterior:</span><span class="sxs-lookup"><span data-stu-id="7ba28-297">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="7ba28-298">Crie o arquivo de configuração do Apache.</span><span class="sxs-lookup"><span data-stu-id="7ba28-298">Create the Apache configuration file.</span></span> <span data-ttu-id="7ba28-299">O exemplo a seguir é um arquivo de configuração simplificado ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="7ba28-299">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="7ba28-300">Coloque o arquivo de configuração do Apache no `/etc/httpd/conf.d/` diretório, que é o diretório de configuração padrão do Apache no CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="7ba28-300">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="7ba28-301">Coloque os arquivos do aplicativo no `/var/www/blazorapp` diretório (o local especificado para `DocumentRoot` o no arquivo de configuração).</span><span class="sxs-lookup"><span data-stu-id="7ba28-301">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="7ba28-302">Reinicie o serviço apache.</span><span class="sxs-lookup"><span data-stu-id="7ba28-302">Restart the Apache service.</span></span>

<span data-ttu-id="7ba28-303">Para obter mais informações, consulte [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .</span><span class="sxs-lookup"><span data-stu-id="7ba28-303">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="7ba28-304">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="7ba28-304">GitHub Pages</span></span>

<span data-ttu-id="7ba28-305">Para tratar as regravações de URL, adicione um `wwwroot/404.html` arquivo com um script que manipula o redirecionamento da solicitação para a `index.html` página.</span><span class="sxs-lookup"><span data-stu-id="7ba28-305">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="7ba28-306">Para obter um exemplo, consulte o [ Blazor repositório GitHub do SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="7ba28-306">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="7ba28-307">[Site ativo](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="7ba28-307">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="7ba28-308">Ao usar um site do projeto em vez de um site da organização, atualize a `<base>` marca no `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="7ba28-308">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="7ba28-309">Defina o `href` valor do atributo para o nome do repositório GitHub com uma barra à direita (por exemplo, `/my-repository/` ).</span><span class="sxs-lookup"><span data-stu-id="7ba28-309">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="7ba28-310">No [ Blazor repositório GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), a base `href` é atualizada em publicar pelo arquivo de [ `.github/workflows/main.yml` configuração](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span><span class="sxs-lookup"><span data-stu-id="7ba28-310">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="7ba28-311">O [ Blazor repositório GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) não é proprietário, mantido ou tem suporte do .net Foundation ou da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="7ba28-311">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="7ba28-312">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="7ba28-312">Host configuration values</span></span>

<span data-ttu-id="7ba28-313">os [ Blazor WebAssembly aplicativos](xref:blazor/hosting-models#blazor-webassembly) podem aceitar os seguintes valores de configuração de host como argumentos de linha de comando em tempo de execução no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7ba28-313">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="7ba28-314">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="7ba28-314">Content root</span></span>

<span data-ttu-id="7ba28-315">O `--contentroot` argumento define o caminho absoluto para o diretório que contém os arquivos de conteúdo do aplicativo ([raiz de conteúdo](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="7ba28-315">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="7ba28-316">Nos exemplos a seguir, `/content-root-path` é o caminho raiz do conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba28-316">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="7ba28-317">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="7ba28-317">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="7ba28-318">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="7ba28-318">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="7ba28-319">Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="7ba28-319">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="7ba28-320">Esta configuração é usada quando o aplicativo é executado com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="7ba28-320">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="7ba28-321">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="7ba28-321">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="7ba28-322">Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba28-322">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="7ba28-323">Caminho base</span><span class="sxs-lookup"><span data-stu-id="7ba28-323">Path base</span></span>

<span data-ttu-id="7ba28-324">O `--pathbase` argumento define o caminho base do aplicativo para um aplicativo executado localmente com um caminho de URL relativo não raiz (a `<base>` marca `href` é definida para um caminho diferente `/` de para preparo e produção).</span><span class="sxs-lookup"><span data-stu-id="7ba28-324">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="7ba28-325">Nos exemplos a seguir, `/relative-URL-path` é o caminho base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ba28-325">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="7ba28-326">Para obter mais informações, consulte [caminho base do aplicativo](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="7ba28-326">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7ba28-327">Ao contrário do caminho fornecido ao `href` da tag `<base>`, não inclua uma barra à direita (`/`) ao passar o valor do argumento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="7ba28-327">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="7ba28-328">Se o caminho base do aplicativo for fornecido na tag `<base>` como `<base href="/CoolApp/">` (inclui uma barra à direita), passe o valor do argumento de linha de comando como `--pathbase=/CoolApp` (nenhuma barra à direita).</span><span class="sxs-lookup"><span data-stu-id="7ba28-328">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="7ba28-329">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="7ba28-329">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="7ba28-330">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="7ba28-330">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="7ba28-331">Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="7ba28-331">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="7ba28-332">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="7ba28-332">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="7ba28-333">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="7ba28-333">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="7ba28-334">Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba28-334">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="7ba28-335">URLs</span><span class="sxs-lookup"><span data-stu-id="7ba28-335">URLs</span></span>

<span data-ttu-id="7ba28-336">O argumento `--urls` define os endereços IP ou os endereços de host com portas e protocolos para escutar solicitações.</span><span class="sxs-lookup"><span data-stu-id="7ba28-336">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="7ba28-337">Passe o argumento ao executar o aplicativo localmente em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="7ba28-337">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="7ba28-338">No diretório do aplicativo, execute:</span><span class="sxs-lookup"><span data-stu-id="7ba28-338">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="7ba28-339">Adicione uma entrada ao arquivo do aplicativo `launchSettings.json` no perfil de **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="7ba28-339">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="7ba28-340">Esta configuração é usada ao executar o aplicativo com o Depurador do Visual Studio e em um prompt de comando com `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="7ba28-340">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="7ba28-341">No Visual Studio, especifique o argumento em **Propriedades**  >  **depurar**  >  **argumentos do aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="7ba28-341">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="7ba28-342">Definir o argumento na página de propriedades do Visual Studio adiciona o argumento ao `launchSettings.json` arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba28-342">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="7ba28-343">Configurar o vinculador</span><span class="sxs-lookup"><span data-stu-id="7ba28-343">Configure the Linker</span></span>

<span data-ttu-id="7ba28-344">Blazorexecuta a vinculação de IL (linguagem intermediária) em cada Build de versão para remover o IL desnecessário dos assemblies de saída.</span><span class="sxs-lookup"><span data-stu-id="7ba28-344">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="7ba28-345">Para obter mais informações, consulte <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="7ba28-345">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="7ba28-346">Carregamento de recurso de inicialização personalizada</span><span class="sxs-lookup"><span data-stu-id="7ba28-346">Custom boot resource loading</span></span>

<span data-ttu-id="7ba28-347">Um Blazor WebAssembly aplicativo pode ser inicializado com a `loadBootResource` função para substituir o mecanismo de carregamento do recurso de inicialização interno.</span><span class="sxs-lookup"><span data-stu-id="7ba28-347">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="7ba28-348">Use `loadBootResource` para os seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="7ba28-348">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="7ba28-349">Permitir que os usuários carreguem recursos estáticos, como dados de fuso horário ou `dotnet.wasm` de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="7ba28-349">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="7ba28-350">Carregue assemblies compactados usando uma solicitação HTTP e descompacte-os no cliente para hosts que não dão suporte à busca de conteúdo compactado do servidor.</span><span class="sxs-lookup"><span data-stu-id="7ba28-350">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="7ba28-351">Recursos de alias para um nome diferente redirecionando cada `fetch` solicitação para um novo nome.</span><span class="sxs-lookup"><span data-stu-id="7ba28-351">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="7ba28-352">`loadBootResource`os parâmetros aparecem na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="7ba28-352">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="7ba28-353">Parâmetro</span><span class="sxs-lookup"><span data-stu-id="7ba28-353">Parameter</span></span>    | <span data-ttu-id="7ba28-354">Descrição</span><span class="sxs-lookup"><span data-stu-id="7ba28-354">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="7ba28-355">Tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="7ba28-355">The type of the resource.</span></span> <span data-ttu-id="7ba28-356">Tipos permissiváveis: `assembly` ,, `pdb` , `dotnetjs` `dotnetwasm` ,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="7ba28-356">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="7ba28-357">O nome do recurso.</span><span class="sxs-lookup"><span data-stu-id="7ba28-357">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="7ba28-358">O URI relativo ou absoluto do recurso.</span><span class="sxs-lookup"><span data-stu-id="7ba28-358">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="7ba28-359">A cadeia de caracteres de integridade que representa o conteúdo esperado na resposta.</span><span class="sxs-lookup"><span data-stu-id="7ba28-359">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="7ba28-360">`loadBootResource`retorna qualquer um dos seguintes para substituir o processo de carregamento:</span><span class="sxs-lookup"><span data-stu-id="7ba28-360">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="7ba28-361">Cadeia de caracteres de URI.</span><span class="sxs-lookup"><span data-stu-id="7ba28-361">URI string.</span></span> <span data-ttu-id="7ba28-362">No exemplo a seguir ( `wwwroot/index.html` ), os seguintes arquivos são atendidos de uma CDN em `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="7ba28-362">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="7ba28-363">Dados de fuso horário</span><span class="sxs-lookup"><span data-stu-id="7ba28-363">Timezone data</span></span>

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

* <span data-ttu-id="7ba28-364">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="7ba28-364">`Promise<Response>`.</span></span> <span data-ttu-id="7ba28-365">Passe o `integrity` parâmetro em um cabeçalho para manter o comportamento de verificação de integridade padrão.</span><span class="sxs-lookup"><span data-stu-id="7ba28-365">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="7ba28-366">O exemplo a seguir ( `wwwroot/index.html` ) adiciona um cabeçalho HTTP personalizado às solicitações de saída e passa o `integrity` parâmetro para a `fetch` chamada:</span><span class="sxs-lookup"><span data-stu-id="7ba28-366">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="7ba28-367">`null`/`undefined`, o que resulta no comportamento de carregamento padrão.</span><span class="sxs-lookup"><span data-stu-id="7ba28-367">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="7ba28-368">As fontes externas devem retornar os cabeçalhos CORS necessários para que os navegadores permitam o carregamento de recursos entre origens.</span><span class="sxs-lookup"><span data-stu-id="7ba28-368">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="7ba28-369">O CDNs geralmente fornece os cabeçalhos necessários por padrão.</span><span class="sxs-lookup"><span data-stu-id="7ba28-369">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="7ba28-370">Você só precisa especificar tipos para comportamentos personalizados.</span><span class="sxs-lookup"><span data-stu-id="7ba28-370">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="7ba28-371">Tipos não especificados para `loadBootResource` são carregados pela estrutura de acordo com seus comportamentos de carregamento padrão.</span><span class="sxs-lookup"><span data-stu-id="7ba28-371">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="7ba28-372">Alterar a extensão do nome de arquivo de arquivos DLL</span><span class="sxs-lookup"><span data-stu-id="7ba28-372">Change the filename extension of DLL files</span></span>

<span data-ttu-id="7ba28-373">Caso você tenha a necessidade de alterar as extensões de nome de arquivo dos arquivos publicados do aplicativo `.dll` , siga as orientações nesta seção.</span><span class="sxs-lookup"><span data-stu-id="7ba28-373">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="7ba28-374">Depois de publicar o aplicativo, use um script de shell ou o pipeline de compilação DevOps para renomear `.dll` arquivos para usar uma extensão de arquivo diferente.</span><span class="sxs-lookup"><span data-stu-id="7ba28-374">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="7ba28-375">Direcione os `.dll` arquivos no `wwwroot` diretório da saída publicada do aplicativo (por exemplo, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="7ba28-375">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="7ba28-376">Nos exemplos a seguir, `.dll` os arquivos são renomeados para usar a `.bin` extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ba28-376">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="7ba28-377">No Windows:</span><span class="sxs-lookup"><span data-stu-id="7ba28-377">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="7ba28-378">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7ba28-378">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="7ba28-379">No Linux ou macOS:</span><span class="sxs-lookup"><span data-stu-id="7ba28-379">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="7ba28-380">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7ba28-380">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="7ba28-381">Para usar uma extensão de arquivo diferente da `.bin` , substitua `.bin` os comandos anteriores.</span><span class="sxs-lookup"><span data-stu-id="7ba28-381">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="7ba28-382">Para resolver os arquivos compactados `blazor.boot.json.gz` `blazor.boot.json.br` , adote uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="7ba28-382">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="7ba28-383">Remova os arquivos compactados `blazor.boot.json.gz` `blazor.boot.json.br` .</span><span class="sxs-lookup"><span data-stu-id="7ba28-383">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="7ba28-384">A compactação está desabilitada com essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="7ba28-384">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="7ba28-385">Recompacte o `blazor.boot.json` arquivo atualizado.</span><span class="sxs-lookup"><span data-stu-id="7ba28-385">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="7ba28-386">As diretrizes anteriores também se aplicam quando os ativos de trabalho do serviço estão em uso.</span><span class="sxs-lookup"><span data-stu-id="7ba28-386">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="7ba28-387">Remova ou recompacte `wwwroot/service-worker-assets.js.br` e `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="7ba28-387">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="7ba28-388">Caso contrário, as verificações de integridade de arquivo falharão no navegador.</span><span class="sxs-lookup"><span data-stu-id="7ba28-388">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="7ba28-389">O exemplo de Windows a seguir usa um script do PowerShell colocado na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="7ba28-389">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="7ba28-390">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="7ba28-390">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="7ba28-391">Se os ativos de trabalho do serviço também estiverem em uso, adicione o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7ba28-391">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="7ba28-392">No arquivo de projeto, o script é executado após a publicação do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7ba28-392">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="7ba28-393">Para fornecer comentários, visite [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="7ba28-393">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
