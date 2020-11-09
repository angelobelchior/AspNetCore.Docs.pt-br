---
title: Arquivos estáticos no ASP.NET Core
author: rick-anderson
description: Saiba como fornecer e proteger arquivos estáticos e configurar comportamentos do middleware de hospedagem de arquivos estáticos em um aplicativo Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/static-files
ms.openlocfilehash: 2e25af03a8a6aaff5b343885711c6ebb68340fac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057850"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="befcc-103">Arquivos estáticos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="befcc-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="befcc-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="befcc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="befcc-105">Arquivos estáticos, como HTML, CSS, imagens e JavaScript, são ativos que um aplicativo ASP.NET Core atende diretamente aos clientes por padrão.</span><span class="sxs-lookup"><span data-stu-id="befcc-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="befcc-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="befcc-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="befcc-107">Fornecer arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="befcc-107">Serve static files</span></span>

<span data-ttu-id="befcc-108">Os arquivos estáticos são armazenados no diretório [raiz da Web](xref:fundamentals/index#web-root) do projeto.</span><span class="sxs-lookup"><span data-stu-id="befcc-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="befcc-109">O diretório padrão é `{content root}/wwwroot` , mas pode ser alterado com o <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> método.</span><span class="sxs-lookup"><span data-stu-id="befcc-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="befcc-110">Para obter mais informações, consulte [raiz de conteúdo](xref:fundamentals/index#content-root) e [raiz da Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="befcc-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="befcc-111">O método <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> define a raiz do conteúdo como o diretório atual:</span><span class="sxs-lookup"><span data-stu-id="befcc-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="befcc-112">O código anterior foi criado com o modelo de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="befcc-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="befcc-113">Os arquivos estáticos podem ser acessados por meio de um caminho relativo à [raiz da Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="befcc-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="befcc-114">Por exemplo, os modelos de projeto de **aplicativo Web** contêm várias pastas dentro da `wwwroot` pasta:</span><span class="sxs-lookup"><span data-stu-id="befcc-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="befcc-115">Considere criar a pasta *wwwroot/images* e adicionar o arquivo *wwwroot/images/MyImage.jpg* .</span><span class="sxs-lookup"><span data-stu-id="befcc-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="befcc-116">O formato do URI para acessar um arquivo na `images` pasta é `https://<hostname>/images/<image_file_name>` .</span><span class="sxs-lookup"><span data-stu-id="befcc-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="befcc-117">Por exemplo, `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="befcc-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="befcc-118">Fornecer arquivos na raiz da Web</span><span class="sxs-lookup"><span data-stu-id="befcc-118">Serve files in web root</span></span>

<span data-ttu-id="befcc-119">Os modelos de aplicativo Web padrão chamam o <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> método no `Startup.Configure` , que permite que os arquivos estáticos sejam atendidos:</span><span class="sxs-lookup"><span data-stu-id="befcc-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="befcc-120">A sobrecarga do `UseStaticFiles` método sem parâmetros marca os arquivos na [raiz da Web](xref:fundamentals/index#web-root) como servable.</span><span class="sxs-lookup"><span data-stu-id="befcc-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="befcc-121">A marcação a seguir referencia *wwwroot/images/MyImage.jpg* :</span><span class="sxs-lookup"><span data-stu-id="befcc-121">The following markup references *wwwroot/images/MyImage.jpg* :</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="befcc-122">No código anterior, o caractere til `~/` aponta para a [raiz da Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="befcc-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="befcc-123">Fornecer arquivos fora do diretório base</span><span class="sxs-lookup"><span data-stu-id="befcc-123">Serve files outside of web root</span></span>

<span data-ttu-id="befcc-124">Considere uma hierarquia de diretório na qual os arquivos estáticos a serem servidos residem fora da [raiz da Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="befcc-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="befcc-125">Uma solicitação pode acessar o `red-rose.jpg` arquivo Configurando o middleware de arquivo estático da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="befcc-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="befcc-126">No código anterior, a hierarquia de diretórios *MyStaticFiles* é exposta publicamente por meio do segmento do URI *StaticFiles* .</span><span class="sxs-lookup"><span data-stu-id="befcc-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="befcc-127">Uma solicitação para `https://<hostname>/StaticFiles/images/red-rose.jpg` servir o arquivo de *red-rose.jpg* .</span><span class="sxs-lookup"><span data-stu-id="befcc-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="befcc-128">A marcação a seguir faz referência a *MyStaticFiles/images/red-rose.jpg* :</span><span class="sxs-lookup"><span data-stu-id="befcc-128">The following markup references *MyStaticFiles/images/red-rose.jpg* :</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="befcc-129">Definir cabeçalhos de resposta HTTP</span><span class="sxs-lookup"><span data-stu-id="befcc-129">Set HTTP response headers</span></span>

<span data-ttu-id="befcc-130">Um <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> objeto pode ser usado para definir cabeçalhos de resposta http.</span><span class="sxs-lookup"><span data-stu-id="befcc-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="befcc-131">Além de configurar o serviço de arquivos estáticos na [raiz da Web](xref:fundamentals/index#web-root), o código a seguir define o `Cache-Control` cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="befcc-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="befcc-132">Os arquivos estáticos são publicamente armazenáveis em cache por 600 segundos:</span><span class="sxs-lookup"><span data-stu-id="befcc-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Cabeçalho de resposta mostrando que o cabeçalho Cache-Control foi adicionado](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="befcc-134">Autorização de arquivo estático</span><span class="sxs-lookup"><span data-stu-id="befcc-134">Static file authorization</span></span>

<span data-ttu-id="befcc-135">O middleware de arquivos estáticos não fornece verificações de autorização.</span><span class="sxs-lookup"><span data-stu-id="befcc-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="befcc-136">Todos os arquivos servidos por ele, incluindo aqueles em `wwwroot` , estão publicamente acessíveis.</span><span class="sxs-lookup"><span data-stu-id="befcc-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="befcc-137">Para fornecer arquivos com base na autorização:</span><span class="sxs-lookup"><span data-stu-id="befcc-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="befcc-138">Armazene-os fora do `wwwroot` e de qualquer diretório acessível ao middleware de arquivo estático padrão.</span><span class="sxs-lookup"><span data-stu-id="befcc-138">Store them outside of `wwwroot` and any directory accessible to the default Static File Middleware.</span></span>
* <span data-ttu-id="befcc-139">Chame `UseStaticFiles` após `UseAuthorization` e especifique o caminho:</span><span class="sxs-lookup"><span data-stu-id="befcc-139">Call `UseStaticFiles` after `UseAuthorization` and specify the path:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2)]
  
  <span data-ttu-id="befcc-140">A abordagem anterior exige que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="befcc-140">The preceding approach requires users to be authenticated:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-99)]

   [!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="befcc-141">Uma abordagem alternativa para fornecer arquivos com base na autorização:</span><span class="sxs-lookup"><span data-stu-id="befcc-141">An alternative approach to serve files based on authorization:</span></span>

* <span data-ttu-id="befcc-142">Armazene-os fora do `wwwroot` e de qualquer diretório acessível ao middleware de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="befcc-142">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="befcc-143">Sirva-os por meio de um método de ação ao qual a autorização é aplicada e retorna um <xref:Microsoft.AspNetCore.Mvc.FileResult> objeto:</span><span class="sxs-lookup"><span data-stu-id="befcc-143">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="befcc-144">Pesquisa no diretório</span><span class="sxs-lookup"><span data-stu-id="befcc-144">Directory browsing</span></span>

<span data-ttu-id="befcc-145">A pesquisa no diretório permite a listagem de diretório em diretórios especificados.</span><span class="sxs-lookup"><span data-stu-id="befcc-145">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="befcc-146">A pesquisa no diretório é desabilitada por padrão por motivos de segurança.</span><span class="sxs-lookup"><span data-stu-id="befcc-146">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="befcc-147">Para obter mais informações, consulte [considerações](#considerations).</span><span class="sxs-lookup"><span data-stu-id="befcc-147">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="befcc-148">Habilitar pesquisa no diretório com:</span><span class="sxs-lookup"><span data-stu-id="befcc-148">Enable directory browsing with:</span></span>

* <span data-ttu-id="befcc-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="befcc-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="befcc-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> em `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="befcc-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="befcc-151">O código anterior permite a navegação no diretório da pasta *wwwroot/images* usando a URL `https://<hostname>/MyImages` , com links para cada arquivo e pasta:</span><span class="sxs-lookup"><span data-stu-id="befcc-151">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![navegação no diretório](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="befcc-153">Fornecer documentos padrão</span><span class="sxs-lookup"><span data-stu-id="befcc-153">Serve default documents</span></span>

<span data-ttu-id="befcc-154">A definição de uma página padrão fornece aos visitantes um ponto de partida em um site.</span><span class="sxs-lookup"><span data-stu-id="befcc-154">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="befcc-155">Para servir uma página padrão de `wwwroot` sem um URI totalmente qualificado, chame o <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> método:</span><span class="sxs-lookup"><span data-stu-id="befcc-155">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="befcc-156">`UseDefaultFiles` deve ser chamado antes de `UseStaticFiles` para fornecer o arquivo padrão.</span><span class="sxs-lookup"><span data-stu-id="befcc-156">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="befcc-157">`UseDefaultFiles` é um regravador de URL que não atende ao arquivo.</span><span class="sxs-lookup"><span data-stu-id="befcc-157">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="befcc-158">Com `UseDefaultFiles` o, solicitações para uma pasta na `wwwroot` pesquisa por:</span><span class="sxs-lookup"><span data-stu-id="befcc-158">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="befcc-159">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="befcc-159">*default.htm*</span></span>
* <span data-ttu-id="befcc-160">*default.html*</span><span class="sxs-lookup"><span data-stu-id="befcc-160">*default.html*</span></span>
* <span data-ttu-id="befcc-161">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="befcc-161">*index.htm*</span></span>
* <span data-ttu-id="befcc-162">*index.html*</span><span class="sxs-lookup"><span data-stu-id="befcc-162">*index.html*</span></span>

<span data-ttu-id="befcc-163">O primeiro arquivo encontrado na lista é fornecido como se a solicitação fosse o URI totalmente qualificado.</span><span class="sxs-lookup"><span data-stu-id="befcc-163">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="befcc-164">A URL do navegador continua refletindo o URI solicitado.</span><span class="sxs-lookup"><span data-stu-id="befcc-164">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="befcc-165">O seguinte código altera o nome de arquivo padrão para *mydefault.html* :</span><span class="sxs-lookup"><span data-stu-id="befcc-165">The following code changes the default file name to *mydefault.html* :</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="befcc-166">O código a seguir mostra `Startup.Configure` o código anterior:</span><span class="sxs-lookup"><span data-stu-id="befcc-166">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="befcc-167">UseFileServer para documentos padrão</span><span class="sxs-lookup"><span data-stu-id="befcc-167">UseFileServer for default documents</span></span>

<span data-ttu-id="befcc-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina a funcionalidade de `UseStaticFiles` , `UseDefaultFiles` e opcionalmente `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="befcc-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="befcc-169">Chame `app.UseFileServer` para habilitar o serviço de arquivos estáticos e o arquivo padrão.</span><span class="sxs-lookup"><span data-stu-id="befcc-169">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="befcc-170">A navegação no diretório não está habilitada.</span><span class="sxs-lookup"><span data-stu-id="befcc-170">Directory browsing isn't enabled.</span></span> <span data-ttu-id="befcc-171">O código a seguir `Startup.Configure` mostra `UseFileServer` :</span><span class="sxs-lookup"><span data-stu-id="befcc-171">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="befcc-172">O código a seguir permite o serviço de arquivos estáticos, o arquivo padrão e a pesquisa no diretório:</span><span class="sxs-lookup"><span data-stu-id="befcc-172">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="befcc-173">O código a seguir mostra `Startup.Configure` o código anterior:</span><span class="sxs-lookup"><span data-stu-id="befcc-173">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="befcc-174">Considere a seguinte hierarquia de diretórios:</span><span class="sxs-lookup"><span data-stu-id="befcc-174">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="befcc-175">O código a seguir permite o serviço de arquivos estáticos, o arquivo padrão e a pesquisa no diretório `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="befcc-175">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="befcc-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> deve ser chamado quando o `EnableDirectoryBrowsing` valor da propriedade é `true` .</span><span class="sxs-lookup"><span data-stu-id="befcc-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="befcc-177">Com o uso da hierarquia de arquivos e do código anterior, as URLs são resolvidas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="befcc-177">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="befcc-178">URI</span><span class="sxs-lookup"><span data-stu-id="befcc-178">URI</span></span>            |      <span data-ttu-id="befcc-179">Resposta</span><span class="sxs-lookup"><span data-stu-id="befcc-179">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="befcc-180">*MyStaticFiles/imagens/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="befcc-180">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="befcc-181">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="befcc-181">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="befcc-182">Se nenhum arquivo nomeado padrão existir no diretório *MyStaticFiles* , `https://<hostname>/StaticFiles` o retornará a listagem de diretório com links clicáveis:</span><span class="sxs-lookup"><span data-stu-id="befcc-182">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Lista de arquivos estáticos](static-files/_static/db2.png)

<span data-ttu-id="befcc-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> e <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> Execute um redirecionamento do lado do cliente a partir do URI de destino sem uma trilha `/`  para o URI de destino com uma à direita `/` .</span><span class="sxs-lookup"><span data-stu-id="befcc-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="befcc-185">Por exemplo, de `https://<hostname>/StaticFiles` para `https://<hostname>/StaticFiles/` .</span><span class="sxs-lookup"><span data-stu-id="befcc-185">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="befcc-186">As URLs relativas no diretório *StaticFiles* são inválidas sem uma barra à direita ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="befcc-186">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="befcc-187">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="befcc-187">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="befcc-188">A <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> classe contém uma `Mappings` propriedade que serve como um mapeamento de extensões de arquivo para tipos de conteúdo MIME.</span><span class="sxs-lookup"><span data-stu-id="befcc-188">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="befcc-189">No exemplo a seguir, várias extensões de arquivo são mapeadas para tipos MIME conhecidos.</span><span class="sxs-lookup"><span data-stu-id="befcc-189">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="befcc-190">A extensão *. rtf* é substituída e *. mp4* é removido:</span><span class="sxs-lookup"><span data-stu-id="befcc-190">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="befcc-191">O código a seguir mostra `Startup.Configure` o código anterior:</span><span class="sxs-lookup"><span data-stu-id="befcc-191">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="befcc-192">Consulte [Tipos de conteúdo MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="befcc-192">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="befcc-193">Tipos de conteúdo não padrão</span><span class="sxs-lookup"><span data-stu-id="befcc-193">Non-standard content types</span></span>

<span data-ttu-id="befcc-194">O middleware de arquivo estático compreende quase 400 tipos de conteúdo de arquivo conhecidos.</span><span class="sxs-lookup"><span data-stu-id="befcc-194">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="befcc-195">Se o usuário solicitar um arquivo com um tipo de arquivo desconhecido, o middleware de arquivo estático passará a solicitação para o próximo middleware no pipeline.</span><span class="sxs-lookup"><span data-stu-id="befcc-195">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="befcc-196">Se nenhum middleware manipular a solicitação, uma resposta *404 Não Encontrado* será retornada.</span><span class="sxs-lookup"><span data-stu-id="befcc-196">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="befcc-197">Se a navegação no diretório estiver habilitada, um link para o arquivo será exibido na lista do diretório.</span><span class="sxs-lookup"><span data-stu-id="befcc-197">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="befcc-198">O seguinte código habilita o fornecimento de tipos desconhecidos e renderiza o arquivo desconhecido como uma imagem:</span><span class="sxs-lookup"><span data-stu-id="befcc-198">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="befcc-199">O código a seguir mostra `Startup.Configure` o código anterior:</span><span class="sxs-lookup"><span data-stu-id="befcc-199">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="befcc-200">Com o código anterior, uma solicitação para um arquivo com um tipo de conteúdo desconhecido é retornada como uma imagem.</span><span class="sxs-lookup"><span data-stu-id="befcc-200">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="befcc-201">A habilitação <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> é um risco à segurança.</span><span class="sxs-lookup"><span data-stu-id="befcc-201">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="befcc-202">Ela está desabilitada por padrão, e seu uso não é recomendado.</span><span class="sxs-lookup"><span data-stu-id="befcc-202">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="befcc-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fornece uma alternativa mais segura para o fornecimento de arquivos com extensões não padrão.</span><span class="sxs-lookup"><span data-stu-id="befcc-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="befcc-204">Fornecer arquivos de vários locais</span><span class="sxs-lookup"><span data-stu-id="befcc-204">Serve files from multiple locations</span></span>

<span data-ttu-id="befcc-205">`UseStaticFiles` e `UseFileServer` o padrão para o provedor de arquivos que aponta para `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="befcc-205">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="befcc-206">Instâncias adicionais do `UseStaticFiles` e do `UseFileServer` podem ser fornecidas com outros provedores de arquivos para fornecer arquivos de outros locais.</span><span class="sxs-lookup"><span data-stu-id="befcc-206">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="befcc-207">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="befcc-207">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="befcc-208">Considerações de segurança para arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="befcc-208">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="befcc-209">`UseDirectoryBrowser` e `UseStaticFiles` podem causar a perda de segredos.</span><span class="sxs-lookup"><span data-stu-id="befcc-209">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="befcc-210">A desabilitação da navegação no diretório em produção é altamente recomendada.</span><span class="sxs-lookup"><span data-stu-id="befcc-210">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="befcc-211">Examine com atenção os diretórios que são habilitados por meio de `UseStaticFiles` ou `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="befcc-211">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="befcc-212">Todo o diretório e seus subdiretórios se tornam publicamente acessíveis.</span><span class="sxs-lookup"><span data-stu-id="befcc-212">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="befcc-213">Armazene arquivos adequados para servir ao público em um diretório dedicado, como `<content_root>/wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="befcc-213">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="befcc-214">Separe esses arquivos de exibições do MVC, Razor páginas, arquivos de configuração, etc.</span><span class="sxs-lookup"><span data-stu-id="befcc-214">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="befcc-215">As URLs para o conteúdo exposto com `UseDirectoryBrowser` e `UseStaticFiles` estão sujeitas à diferenciação de maiúsculas e minúsculas e a restrições de caracteres do sistema de arquivos subjacente.</span><span class="sxs-lookup"><span data-stu-id="befcc-215">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="befcc-216">Por exemplo, o Windows não diferencia maiúsculas de minúsculas, mas o macOS e o Linux não são.</span><span class="sxs-lookup"><span data-stu-id="befcc-216">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="befcc-217">Os aplicativos ASP.NET Core hospedados no IIS usam o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para encaminhar todas as solicitações ao aplicativo, inclusive as solicitações de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="befcc-217">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="befcc-218">O manipulador de arquivos estáticos do IIS não é usado e não tem a chance de lidar com solicitações.</span><span class="sxs-lookup"><span data-stu-id="befcc-218">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="befcc-219">Conclua as etapas seguinte no Gerenciador do IIS para remover o manipulador de arquivos estáticos no IIS no nível do servidor ou do site:</span><span class="sxs-lookup"><span data-stu-id="befcc-219">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="befcc-220">Navegue para o recurso **Módulos** .</span><span class="sxs-lookup"><span data-stu-id="befcc-220">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="befcc-221">Selecione **StaticFileModule** na lista.</span><span class="sxs-lookup"><span data-stu-id="befcc-221">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="befcc-222">Clique em **Remover** na barra lateral **Ações** .</span><span class="sxs-lookup"><span data-stu-id="befcc-222">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="befcc-223">Se o manipulador de arquivo estático do IIS estiver habilitado **e** o Módulo do ASP.NET Core não estiver configurado corretamente, os arquivos estáticos serão atendidos.</span><span class="sxs-lookup"><span data-stu-id="befcc-223">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="befcc-224">Isso acontece, por exemplo, se o arquivo *web.config* não foi implantado.</span><span class="sxs-lookup"><span data-stu-id="befcc-224">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="befcc-225">Coloque os arquivos de código, incluindo *. cs* e *. cshtml* , fora da [raiz da Web](xref:fundamentals/index#web-root)do projeto de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="befcc-225">Place code files, including *.cs* and *.cshtml* , outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="befcc-226">Portanto, uma separação lógica é criada entre o conteúdo do lado do cliente do aplicativo e o código baseado em servidor.</span><span class="sxs-lookup"><span data-stu-id="befcc-226">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="befcc-227">Isso impede a perda de código do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="befcc-227">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="befcc-228">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="befcc-228">Additional resources</span></span>

* [<span data-ttu-id="befcc-229">Middleware</span><span class="sxs-lookup"><span data-stu-id="befcc-229">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="befcc-230">Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="befcc-230">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="befcc-231">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="befcc-231">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="befcc-232">Arquivos estáticos, como HTML, CSS, imagens e JavaScript, são ativos que um aplicativo ASP.NET Core fornece diretamente para os clientes.</span><span class="sxs-lookup"><span data-stu-id="befcc-232">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="befcc-233">Algumas etapas de configuração são necessárias para habilitar o fornecimento desses arquivos.</span><span class="sxs-lookup"><span data-stu-id="befcc-233">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="befcc-234">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="befcc-234">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="befcc-235">Fornecer arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="befcc-235">Serve static files</span></span>

<span data-ttu-id="befcc-236">Os arquivos estáticos são armazenados no diretório [raiz da Web](xref:fundamentals/index#web-root) do projeto.</span><span class="sxs-lookup"><span data-stu-id="befcc-236">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="befcc-237">O diretório padrão é *{Content root}/wwwroot* , mas pode ser alterado por meio do <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> método.</span><span class="sxs-lookup"><span data-stu-id="befcc-237">The default directory is *{content root}/wwwroot* , but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="befcc-238">Consulte [Raiz de conteúdo](xref:fundamentals/index#content-root) e [Diretório base](xref:fundamentals/index#web-root) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="befcc-238">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="befcc-239">O host Web do aplicativo deve ser informado do diretório raiz do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="befcc-239">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="befcc-240">O método `WebHost.CreateDefaultBuilder` define a raiz do conteúdo como o diretório atual:</span><span class="sxs-lookup"><span data-stu-id="befcc-240">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="befcc-241">Os arquivos estáticos podem ser acessados por meio de um caminho relativo à [raiz da Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="befcc-241">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="befcc-242">Por exemplo, o modelo de projeto de **aplicativo Web** contém várias pastas dentro da `wwwroot` pasta:</span><span class="sxs-lookup"><span data-stu-id="befcc-242">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="befcc-243">O formato do URI para acessar um arquivo na *images* subpasta imagens *é \<server_address> http:// \<image_file_name> /images/* .</span><span class="sxs-lookup"><span data-stu-id="befcc-243">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>* .</span></span> <span data-ttu-id="befcc-244">Por exemplo, *http://localhost:9189/images/banner3.svg* .</span><span class="sxs-lookup"><span data-stu-id="befcc-244">For example, *http://localhost:9189/images/banner3.svg* .</span></span>

<span data-ttu-id="befcc-245">Se estiver direcionando o .NET Framework, adicione o pacote [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) ao projeto.</span><span class="sxs-lookup"><span data-stu-id="befcc-245">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="befcc-246">Se você estiver direcionando para o .NET Core, o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) incluirá esse pacote.</span><span class="sxs-lookup"><span data-stu-id="befcc-246">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="befcc-247">Configure o [middleware](xref:fundamentals/middleware/index), que permite o serviço de arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="befcc-247">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="befcc-248">Fornecer arquivos dentro do diretório base</span><span class="sxs-lookup"><span data-stu-id="befcc-248">Serve files inside of web root</span></span>

<span data-ttu-id="befcc-249">Invocar o <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> método em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="befcc-249">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="befcc-250">A sobrecarga do `UseStaticFiles` método sem parâmetros marca os arquivos na [raiz da Web](xref:fundamentals/index#web-root) como servable.</span><span class="sxs-lookup"><span data-stu-id="befcc-250">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="befcc-251">A seguinte marcação referencia *wwwroot/images/banner1.svg* :</span><span class="sxs-lookup"><span data-stu-id="befcc-251">The following markup references *wwwroot/images/banner1.svg* :</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="befcc-252">No código anterior, o caractere til `~/` aponta para a [raiz da Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="befcc-252">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="befcc-253">Fornecer arquivos fora do diretório base</span><span class="sxs-lookup"><span data-stu-id="befcc-253">Serve files outside of web root</span></span>

<span data-ttu-id="befcc-254">Considere uma hierarquia de diretório na qual os arquivos estáticos a serem servidos residem fora da [raiz da Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="befcc-254">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="befcc-255">Uma solicitação pode acessar o arquivo *banner1.svg* configurando o middleware de arquivos estáticos da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="befcc-255">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="befcc-256">No código anterior, a hierarquia de diretórios *MyStaticFiles* é exposta publicamente por meio do segmento do URI *StaticFiles* .</span><span class="sxs-lookup"><span data-stu-id="befcc-256">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="befcc-257">Uma solicitação para *http:// \<server_address> /StaticFiles/images/banner1.svg* serve o arquivo *banner1. svg* .</span><span class="sxs-lookup"><span data-stu-id="befcc-257">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="befcc-258">A seguinte marcação referencia *MyStaticFiles/images/banner1.svg* :</span><span class="sxs-lookup"><span data-stu-id="befcc-258">The following markup references *MyStaticFiles/images/banner1.svg* :</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="befcc-259">Definir cabeçalhos de resposta HTTP</span><span class="sxs-lookup"><span data-stu-id="befcc-259">Set HTTP response headers</span></span>

<span data-ttu-id="befcc-260">Um <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> objeto pode ser usado para definir cabeçalhos de resposta http.</span><span class="sxs-lookup"><span data-stu-id="befcc-260">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="befcc-261">Além de configurar o serviço de arquivos estáticos na [raiz da Web](xref:fundamentals/index#web-root), o código a seguir define o `Cache-Control` cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="befcc-261">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="befcc-262">O <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> método existe no pacote [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .</span><span class="sxs-lookup"><span data-stu-id="befcc-262">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="befcc-263">Os arquivos se tornaram armazenáveis em cache publicamente por 10 minutos (600 segundos) no ambiente de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="befcc-263">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Cabeçalho de resposta mostrando que o cabeçalho Cache-Control foi adicionado](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="befcc-265">Autorização de arquivo estático</span><span class="sxs-lookup"><span data-stu-id="befcc-265">Static file authorization</span></span>

<span data-ttu-id="befcc-266">O middleware de arquivos estáticos não fornece verificações de autorização.</span><span class="sxs-lookup"><span data-stu-id="befcc-266">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="befcc-267">Todos os arquivos atendidos, incluindo aqueles em *wwwroot* , estão acessíveis publicamente.</span><span class="sxs-lookup"><span data-stu-id="befcc-267">Any files served by it, including those under *wwwroot* , are publicly accessible.</span></span> <span data-ttu-id="befcc-268">Para fornecer arquivos com base na autorização:</span><span class="sxs-lookup"><span data-stu-id="befcc-268">To serve files based on authorization:</span></span>

* <span data-ttu-id="befcc-269">Armazene-os fora do *wwwroot* e de qualquer diretório acessível ao middleware de arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="befcc-269">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="befcc-270">Forneça-os por meio de um método de ação ao qual a autorização é aplicada.</span><span class="sxs-lookup"><span data-stu-id="befcc-270">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="befcc-271">Retornar um <xref:Microsoft.AspNetCore.Mvc.FileResult> objeto:</span><span class="sxs-lookup"><span data-stu-id="befcc-271">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="befcc-272">Habilitar navegação no diretório</span><span class="sxs-lookup"><span data-stu-id="befcc-272">Enable directory browsing</span></span>

<span data-ttu-id="befcc-273">A navegação no diretório permite que os usuários do aplicativo Web vejam uma listagem de diretório e arquivos em um diretório especificado.</span><span class="sxs-lookup"><span data-stu-id="befcc-273">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="befcc-274">A navegação no diretório está desabilitada por padrão por motivos de segurança (consulte [Considerações](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="befcc-274">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="befcc-275">Habilite a pesquisa no diretório invocando o <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> método em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="befcc-275">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="befcc-276">Adicione os serviços necessários invocando o <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> método de `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="befcc-276">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="befcc-277">O código anterior permite a navegação no diretório da pasta *wwwroot/images* usando a URL *http:// \<server_address> /myImages* , com links para cada arquivo e pasta:</span><span class="sxs-lookup"><span data-stu-id="befcc-277">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages* , with links to each file and folder:</span></span>

![navegação no diretório](static-files/_static/dir-browse.png)

<span data-ttu-id="befcc-279">Consulte [Considerações](#considerations) para saber mais sobre os riscos de segurança ao habilitar a navegação.</span><span class="sxs-lookup"><span data-stu-id="befcc-279">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="befcc-280">Observe as duas chamadas `UseStaticFiles` no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="befcc-280">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="befcc-281">A primeira chamada permite o fornecimento de arquivos estáticos na pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="befcc-281">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="befcc-282">A segunda chamada habilita a navegação no diretório da pasta *wwwroot/images* usando a URL *http:// \<server_address> /myImages* :</span><span class="sxs-lookup"><span data-stu-id="befcc-282">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages* :</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="befcc-283">Fornecer um documento padrão</span><span class="sxs-lookup"><span data-stu-id="befcc-283">Serve a default document</span></span>

<span data-ttu-id="befcc-284">Definir uma home page padrão fornece ao visitantes um ponto de partida lógico de ao visitar o site.</span><span class="sxs-lookup"><span data-stu-id="befcc-284">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="befcc-285">Para servir uma página padrão sem que o usuário qualifique totalmente o URI, chame o <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> método de `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="befcc-285">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="befcc-286">`UseDefaultFiles` deve ser chamado antes de `UseStaticFiles` para fornecer o arquivo padrão.</span><span class="sxs-lookup"><span data-stu-id="befcc-286">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="befcc-287">`UseDefaultFiles` é um rewriter de URL que, na verdade, não fornece o arquivo.</span><span class="sxs-lookup"><span data-stu-id="befcc-287">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="befcc-288">Habilite o middleware de arquivos estáticos por meio de `UseStaticFiles` para fornecer o arquivo.</span><span class="sxs-lookup"><span data-stu-id="befcc-288">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="befcc-289">Com `UseDefaultFiles`, as solicitações para uma pasta pesquisam:</span><span class="sxs-lookup"><span data-stu-id="befcc-289">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="befcc-290">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="befcc-290">*default.htm*</span></span>
* <span data-ttu-id="befcc-291">*default.html*</span><span class="sxs-lookup"><span data-stu-id="befcc-291">*default.html*</span></span>
* <span data-ttu-id="befcc-292">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="befcc-292">*index.htm*</span></span>
* <span data-ttu-id="befcc-293">*index.html*</span><span class="sxs-lookup"><span data-stu-id="befcc-293">*index.html*</span></span>

<span data-ttu-id="befcc-294">O primeiro arquivo encontrado na lista é fornecido como se a solicitação fosse o URI totalmente qualificado.</span><span class="sxs-lookup"><span data-stu-id="befcc-294">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="befcc-295">A URL do navegador continua refletindo o URI solicitado.</span><span class="sxs-lookup"><span data-stu-id="befcc-295">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="befcc-296">O seguinte código altera o nome de arquivo padrão para *mydefault.html* :</span><span class="sxs-lookup"><span data-stu-id="befcc-296">The following code changes the default file name to *mydefault.html* :</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="befcc-297">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="befcc-297">UseFileServer</span></span>

<span data-ttu-id="befcc-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combina a funcionalidade de `UseStaticFiles` , `UseDefaultFiles` e opcionalmente `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="befcc-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="befcc-299">O código a seguir permite o fornecimento de arquivos estáticos e do arquivo padrão.</span><span class="sxs-lookup"><span data-stu-id="befcc-299">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="befcc-300">A navegação no diretório não está habilitada.</span><span class="sxs-lookup"><span data-stu-id="befcc-300">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="befcc-301">O seguinte código baseia-se na sobrecarga sem parâmetros, habilitando a navegação no diretório:</span><span class="sxs-lookup"><span data-stu-id="befcc-301">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="befcc-302">Considere a seguinte hierarquia de diretórios:</span><span class="sxs-lookup"><span data-stu-id="befcc-302">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="befcc-303">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="befcc-303">**wwwroot**</span></span>
  * <span data-ttu-id="befcc-304">**CSS**</span><span class="sxs-lookup"><span data-stu-id="befcc-304">**css**</span></span>
  * <span data-ttu-id="befcc-305">**images**</span><span class="sxs-lookup"><span data-stu-id="befcc-305">**images**</span></span>
  * <span data-ttu-id="befcc-306">**JS**</span><span class="sxs-lookup"><span data-stu-id="befcc-306">**js**</span></span>
* <span data-ttu-id="befcc-307">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="befcc-307">**MyStaticFiles**</span></span>
  * <span data-ttu-id="befcc-308">**images**</span><span class="sxs-lookup"><span data-stu-id="befcc-308">**images**</span></span>
    * <span data-ttu-id="befcc-309">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="befcc-309">*banner1.svg*</span></span>
  * <span data-ttu-id="befcc-310">*default.html*</span><span class="sxs-lookup"><span data-stu-id="befcc-310">*default.html*</span></span>

<span data-ttu-id="befcc-311">O seguinte código habilita arquivos estáticos, arquivos padrão e a navegação no diretório de `MyStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="befcc-311">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="befcc-312">`AddDirectoryBrowser` precisa ser chamado quando o valor da propriedade `EnableDirectoryBrowsing` é `true`:</span><span class="sxs-lookup"><span data-stu-id="befcc-312">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="befcc-313">Com o uso da hierarquia de arquivos e do código anterior, as URLs são resolvidas da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="befcc-313">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="befcc-314">URI</span><span class="sxs-lookup"><span data-stu-id="befcc-314">URI</span></span>            |                             <span data-ttu-id="befcc-315">Resposta</span><span class="sxs-lookup"><span data-stu-id="befcc-315">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="befcc-316">*http:// \<server_address> /StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="befcc-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="befcc-317">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="befcc-317">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="befcc-318">*http:// \<server_address> /StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="befcc-318">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="befcc-319">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="befcc-319">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="befcc-320">Se nenhum arquivo nomeado padrão existir no diretório *MyStaticFiles* , *http:// \<server_address> /StaticFiles* retornará a listagem de diretório com links clicáveis:</span><span class="sxs-lookup"><span data-stu-id="befcc-320">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Lista de arquivos estáticos](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="befcc-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> e <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> executam um redirecionamento do lado do cliente de `http://{SERVER ADDRESS}/StaticFiles` (sem uma barra à direita) para `http://{SERVER ADDRESS}/StaticFiles/` (com uma barra à direita).</span><span class="sxs-lookup"><span data-stu-id="befcc-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="befcc-323">As URLs relativas dentro do diretório *StaticFiles* são inválidas sem uma barra "\" à direita.</span><span class="sxs-lookup"><span data-stu-id="befcc-323">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="befcc-324">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="befcc-324">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="befcc-325">A <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> classe contém uma `Mappings` propriedade que serve como um mapeamento de extensões de arquivo para tipos de conteúdo MIME.</span><span class="sxs-lookup"><span data-stu-id="befcc-325">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="befcc-326">Na amostra a seguir, várias extensões de arquivo são registradas para tipos MIME conhecidos.</span><span class="sxs-lookup"><span data-stu-id="befcc-326">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="befcc-327">A extensão *.rtf* é substituída, e *.mp4* é removida.</span><span class="sxs-lookup"><span data-stu-id="befcc-327">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="befcc-328">Consulte [Tipos de conteúdo MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="befcc-328">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="befcc-329">Para obter informações sobre como usar um personalizado <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> ou configurar outros <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> em Blazor aplicativos de servidor, consulte <xref:blazor/fundamentals/additional-scenarios#static-files> .</span><span class="sxs-lookup"><span data-stu-id="befcc-329">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/additional-scenarios#static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="befcc-330">Tipos de conteúdo não padrão</span><span class="sxs-lookup"><span data-stu-id="befcc-330">Non-standard content types</span></span>

<span data-ttu-id="befcc-331">O middleware de arquivo estático compreende quase 400 tipos de conteúdo de arquivo conhecidos.</span><span class="sxs-lookup"><span data-stu-id="befcc-331">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="befcc-332">Se o usuário solicitar um arquivo com um tipo desconhecido, o middleware de arquivo estático passará a solicitação para o próximo middleware no pipeline.</span><span class="sxs-lookup"><span data-stu-id="befcc-332">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="befcc-333">Se nenhum middleware manipular a solicitação, uma resposta *404 Não Encontrado* será retornada.</span><span class="sxs-lookup"><span data-stu-id="befcc-333">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="befcc-334">Se a navegação no diretório estiver habilitada, um link para o arquivo será exibido na lista do diretório.</span><span class="sxs-lookup"><span data-stu-id="befcc-334">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="befcc-335">O seguinte código habilita o fornecimento de tipos desconhecidos e renderiza o arquivo desconhecido como uma imagem:</span><span class="sxs-lookup"><span data-stu-id="befcc-335">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="befcc-336">Com o código anterior, uma solicitação para um arquivo com um tipo de conteúdo desconhecido é retornada como uma imagem.</span><span class="sxs-lookup"><span data-stu-id="befcc-336">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="befcc-337">A habilitação <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> é um risco à segurança.</span><span class="sxs-lookup"><span data-stu-id="befcc-337">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="befcc-338">Ela está desabilitada por padrão, e seu uso não é recomendado.</span><span class="sxs-lookup"><span data-stu-id="befcc-338">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="befcc-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fornece uma alternativa mais segura para o fornecimento de arquivos com extensões não padrão.</span><span class="sxs-lookup"><span data-stu-id="befcc-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="befcc-340">Fornecer arquivos de vários locais</span><span class="sxs-lookup"><span data-stu-id="befcc-340">Serve files from multiple locations</span></span>

<span data-ttu-id="befcc-341">`UseStaticFiles` e `UseFileServer` usa como padrão o provedor de arquivos que aponta em *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="befcc-341">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot* .</span></span> <span data-ttu-id="befcc-342">Você pode fornecer instâncias adicionais do `UseStaticFiles` e `UseFileServer` com outros provedores de arquivos para fornecer arquivos de outros locais.</span><span class="sxs-lookup"><span data-stu-id="befcc-342">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="befcc-343">Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="befcc-343">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="befcc-344">Considerações</span><span class="sxs-lookup"><span data-stu-id="befcc-344">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="befcc-345">`UseDirectoryBrowser` e `UseStaticFiles` podem causar a perda de segredos.</span><span class="sxs-lookup"><span data-stu-id="befcc-345">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="befcc-346">A desabilitação da navegação no diretório em produção é altamente recomendada.</span><span class="sxs-lookup"><span data-stu-id="befcc-346">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="befcc-347">Examine com atenção os diretórios que são habilitados por meio de `UseStaticFiles` ou `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="befcc-347">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="befcc-348">Todo o diretório e seus subdiretórios se tornam publicamente acessíveis.</span><span class="sxs-lookup"><span data-stu-id="befcc-348">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="befcc-349">Armazene arquivos adequados para servir ao público em um diretório dedicado, como *\<content_root> /wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="befcc-349">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot* .</span></span> <span data-ttu-id="befcc-350">Separe esses arquivos de exibições do MVC, Razor páginas (somente 2. x), arquivos de configuração, etc.</span><span class="sxs-lookup"><span data-stu-id="befcc-350">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="befcc-351">As URLs para o conteúdo exposto com `UseDirectoryBrowser` e `UseStaticFiles` estão sujeitas à diferenciação de maiúsculas e minúsculas e a restrições de caracteres do sistema de arquivos subjacente.</span><span class="sxs-lookup"><span data-stu-id="befcc-351">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="befcc-352">Por exemplo, o Windows diferencia maiúsculas de minúsculas, o macOS e o Linux não.</span><span class="sxs-lookup"><span data-stu-id="befcc-352">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="befcc-353">Os aplicativos ASP.NET Core hospedados no IIS usam o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) para encaminhar todas as solicitações ao aplicativo, inclusive as solicitações de arquivo estático.</span><span class="sxs-lookup"><span data-stu-id="befcc-353">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="befcc-354">O manipulador de arquivos estáticos do IIS não é usado.</span><span class="sxs-lookup"><span data-stu-id="befcc-354">The IIS static file handler isn't used.</span></span> <span data-ttu-id="befcc-355">Ele não tem nenhuma possibilidade de manipular as solicitações antes que elas sejam manipuladas pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="befcc-355">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="befcc-356">Conclua as etapas seguinte no Gerenciador do IIS para remover o manipulador de arquivos estáticos no IIS no nível do servidor ou do site:</span><span class="sxs-lookup"><span data-stu-id="befcc-356">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="befcc-357">Navegue para o recurso **Módulos** .</span><span class="sxs-lookup"><span data-stu-id="befcc-357">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="befcc-358">Selecione **StaticFileModule** na lista.</span><span class="sxs-lookup"><span data-stu-id="befcc-358">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="befcc-359">Clique em **Remover** na barra lateral **Ações** .</span><span class="sxs-lookup"><span data-stu-id="befcc-359">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="befcc-360">Se o manipulador de arquivo estático do IIS estiver habilitado **e** o Módulo do ASP.NET Core não estiver configurado corretamente, os arquivos estáticos serão atendidos.</span><span class="sxs-lookup"><span data-stu-id="befcc-360">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="befcc-361">Isso acontece, por exemplo, se o arquivo *web.config* não foi implantado.</span><span class="sxs-lookup"><span data-stu-id="befcc-361">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="befcc-362">Coloque os arquivos de código (incluindo *. cs* e *. cshtml* ) fora da [raiz da Web](xref:fundamentals/index#web-root)do projeto de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="befcc-362">Place code files (including *.cs* and *.cshtml* ) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="befcc-363">Portanto, uma separação lógica é criada entre o conteúdo do lado do cliente do aplicativo e o código baseado em servidor.</span><span class="sxs-lookup"><span data-stu-id="befcc-363">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="befcc-364">Isso impede a perda de código do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="befcc-364">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="befcc-365">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="befcc-365">Additional resources</span></span>

* [<span data-ttu-id="befcc-366">Middleware</span><span class="sxs-lookup"><span data-stu-id="befcc-366">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="befcc-367">Introdução ao ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="befcc-367">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
