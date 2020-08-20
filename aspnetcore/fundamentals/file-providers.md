---
title: Provedores de arquivos no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
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
uid: fundamentals/file-providers
ms.openlocfilehash: 30c28e7bd4cd9c926b157f5a7b9e6688bd5b9b9a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634599"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="5fb02-103">Provedores de arquivos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5fb02-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="5fb02-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5fb02-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5fb02-105">O ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos.</span><span class="sxs-lookup"><span data-stu-id="5fb02-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="5fb02-106">Os provedores de arquivos são usados em todo o ASP.NET Core Framework.</span><span class="sxs-lookup"><span data-stu-id="5fb02-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="5fb02-107">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5fb02-107">For example:</span></span>

* <span data-ttu-id="5fb02-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> expõe a raiz do [conteúdo](xref:fundamentals/index#content-root) do aplicativo e a [raiz da Web](xref:fundamentals/index#web-root) como `IFileProvider` tipos.</span><span class="sxs-lookup"><span data-stu-id="5fb02-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="5fb02-109">O [middleware de arquivos estáticos](xref:fundamentals/static-files) usa provedores de arquivos para localizar arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="5fb02-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="5fb02-110">[Razor](xref:mvc/views/razor) usa provedores de arquivos para localizar páginas e exibições.</span><span class="sxs-lookup"><span data-stu-id="5fb02-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="5fb02-111">As ferramentas do .NET Core usam provedores de arquivos e padrões glob para especificar quais arquivos devem ser publicados.</span><span class="sxs-lookup"><span data-stu-id="5fb02-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="5fb02-112">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5fb02-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="5fb02-113">Interfaces de provedor de arquivo</span><span class="sxs-lookup"><span data-stu-id="5fb02-113">File Provider interfaces</span></span>

<span data-ttu-id="5fb02-114">A interface principal é <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="5fb02-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="5fb02-115">`IFileProvider` expõe métodos para:</span><span class="sxs-lookup"><span data-stu-id="5fb02-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="5fb02-116">Obter informações do arquivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="5fb02-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="5fb02-117">Obter informações do diretório (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="5fb02-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="5fb02-118">Configurar notificações de alteração (usando um <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="5fb02-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="5fb02-119">`IFileInfo` fornece métodos e propriedades para trabalhar com arquivos:</span><span class="sxs-lookup"><span data-stu-id="5fb02-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="5fb02-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (em bytes)</span><span class="sxs-lookup"><span data-stu-id="5fb02-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="5fb02-121">Data de <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="5fb02-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="5fb02-122">Você pode ler o arquivo usando o <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> método.</span><span class="sxs-lookup"><span data-stu-id="5fb02-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="5fb02-123">O aplicativo de exemplo *FileProviderSample* demonstra como configurar um provedor de arquivos no `Startup.ConfigureServices` para uso em todo o aplicativo por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5fb02-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="5fb02-124">Implementações do provedor de arquivos</span><span class="sxs-lookup"><span data-stu-id="5fb02-124">File Provider implementations</span></span>

<span data-ttu-id="5fb02-125">A tabela a seguir lista as implementações do `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="5fb02-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="5fb02-126">Implementação</span><span class="sxs-lookup"><span data-stu-id="5fb02-126">Implementation</span></span> | <span data-ttu-id="5fb02-127">Descrição</span><span class="sxs-lookup"><span data-stu-id="5fb02-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="5fb02-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="5fb02-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="5fb02-129">Usado para fornecer acesso combinado a arquivos e diretórios de um ou mais outros provedores.</span><span class="sxs-lookup"><span data-stu-id="5fb02-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="5fb02-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="5fb02-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="5fb02-131">Usado para acessar arquivos inseridos em assemblies.</span><span class="sxs-lookup"><span data-stu-id="5fb02-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="5fb02-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="5fb02-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="5fb02-133">Usado para acessar os arquivos físicos do sistema.</span><span class="sxs-lookup"><span data-stu-id="5fb02-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="5fb02-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="5fb02-134">PhysicalFileProvider</span></span>

<span data-ttu-id="5fb02-135">O <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fornece acesso ao sistema de arquivos físico.</span><span class="sxs-lookup"><span data-stu-id="5fb02-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="5fb02-136">O `PhysicalFileProvider` usa o tipo <xref:System.IO.File?displayProperty=fullName> (para o provedor físico) e define o escopo de todos os caminhos para um diretório e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="5fb02-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="5fb02-137">Esse escopo impede o acesso ao sistema de arquivos fora do diretório especificado e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="5fb02-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="5fb02-138">O cenário mais comum para criar e usar um `PhysicalFileProvider` é solicitar um `IFileProvider` em um construtor por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5fb02-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5fb02-139">Ao instanciar esse provedor diretamente, um caminho de diretório absoluto é necessário e serve como o caminho base para todas as solicitações feitas usando o provedor.</span><span class="sxs-lookup"><span data-stu-id="5fb02-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="5fb02-140">Não há suporte para padrões glob no caminho do diretório.</span><span class="sxs-lookup"><span data-stu-id="5fb02-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="5fb02-141">O código a seguir mostra como usar o `PhysicalFileProvider` para obter o conteúdo do diretório e as informações do arquivo:</span><span class="sxs-lookup"><span data-stu-id="5fb02-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="5fb02-142">Tipos no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="5fb02-142">Types in the preceding example:</span></span>

* <span data-ttu-id="5fb02-143">`provider` é um `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="5fb02-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="5fb02-144">`contents` é um `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="5fb02-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="5fb02-145">`fileInfo` é um `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="5fb02-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="5fb02-146">O provedor de arquivos pode ser usado para percorrer o diretório especificado por `applicationRoot` ou chamar `GetFileInfo` para obter as informações de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="5fb02-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="5fb02-147">Padrões de glob não podem ser passados para o `GetFileInfo` método.</span><span class="sxs-lookup"><span data-stu-id="5fb02-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="5fb02-148">O provedor de arquivos não tem acesso fora do diretório `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="5fb02-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="5fb02-149">O aplicativo de exemplo *FileProviderSample* cria o provedor no `Startup.ConfigureServices` método usando <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="5fb02-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="5fb02-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="5fb02-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="5fb02-151">O <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> é usado para acessar arquivos inseridos em assemblies.</span><span class="sxs-lookup"><span data-stu-id="5fb02-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="5fb02-152">O `ManifestEmbeddedFileProvider` usa um manifesto compilado no assembly para reconstruir os caminhos originais dos arquivos inseridos.</span><span class="sxs-lookup"><span data-stu-id="5fb02-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="5fb02-153">Para gerar um manifesto dos arquivos inseridos:</span><span class="sxs-lookup"><span data-stu-id="5fb02-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="5fb02-154">Adicione o pacote NuGet [Microsoft. Extensions.](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) . Embedded ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="5fb02-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="5fb02-155">Defina a propriedade `<GenerateEmbeddedFilesManifest>` como `true`.</span><span class="sxs-lookup"><span data-stu-id="5fb02-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="5fb02-156">Especifique os arquivos a serem inseridos [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) :</span><span class="sxs-lookup"><span data-stu-id="5fb02-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="5fb02-157">Use [padrões glob](#glob-patterns) para especificar um ou mais arquivos a serem inseridos no assembly.</span><span class="sxs-lookup"><span data-stu-id="5fb02-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="5fb02-158">O aplicativo de exemplo *FileProviderSample* cria um `ManifestEmbeddedFileProvider` e passa o assembly atualmente em execução para seu construtor.</span><span class="sxs-lookup"><span data-stu-id="5fb02-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="5fb02-159">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="5fb02-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="5fb02-160">Sobrecargas adicionais permitem:</span><span class="sxs-lookup"><span data-stu-id="5fb02-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="5fb02-161">Especificar um caminho de arquivo relativo.</span><span class="sxs-lookup"><span data-stu-id="5fb02-161">Specify a relative file path.</span></span>
* <span data-ttu-id="5fb02-162">Delimitar os arquivos segundo a data da última modificação.</span><span class="sxs-lookup"><span data-stu-id="5fb02-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="5fb02-163">Nomear o recurso inserido que contém o manifesto do arquivo inserido.</span><span class="sxs-lookup"><span data-stu-id="5fb02-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="5fb02-164">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="5fb02-164">Overload</span></span> | <span data-ttu-id="5fb02-165">Descrição</span><span class="sxs-lookup"><span data-stu-id="5fb02-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="5fb02-166">Aceita um parâmetro de caminho relativo `root` opcional.</span><span class="sxs-lookup"><span data-stu-id="5fb02-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="5fb02-167">Especifique o `root` para definir o escopo das chamadas de <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> para esses recursos no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="5fb02-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="5fb02-168">Aceita um parâmetro de caminho relativo `root` opcional e um parâmetro de data `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="5fb02-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="5fb02-169">A data `lastModified` tem como escopo a data da última modificação das instâncias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> retornadas por <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="5fb02-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="5fb02-170">Aceita um caminho relativo `root` opcional, data de `lastModified` e parâmetros `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="5fb02-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="5fb02-171">O `manifestName` representa o nome do recurso inserido que contém o manifesto.</span><span class="sxs-lookup"><span data-stu-id="5fb02-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="5fb02-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="5fb02-172">CompositeFileProvider</span></span>

<span data-ttu-id="5fb02-173">O <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instâncias de `IFileProvider`, expondo uma interface única para trabalhar com arquivos de vários provedores.</span><span class="sxs-lookup"><span data-stu-id="5fb02-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="5fb02-174">Ao criar o `CompositeFileProvider`, passe uma ou mais instâncias de `IFileProvider` para o construtor.</span><span class="sxs-lookup"><span data-stu-id="5fb02-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="5fb02-175">No aplicativo de exemplo *FileProviderSample* , um `PhysicalFileProvider` e um `ManifestEmbeddedFileProvider` fornecem arquivos para um `CompositeFileProvider` registrado no contêiner de serviço do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5fb02-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="5fb02-176">O código a seguir é encontrado no método do projeto `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5fb02-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="5fb02-177">Monitorar as alterações</span><span class="sxs-lookup"><span data-stu-id="5fb02-177">Watch for changes</span></span>

<span data-ttu-id="5fb02-178">O <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> método fornece um cenário para assistir a um ou mais arquivos ou diretórios para alterações.</span><span class="sxs-lookup"><span data-stu-id="5fb02-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="5fb02-179">O método `Watch`:</span><span class="sxs-lookup"><span data-stu-id="5fb02-179">The `Watch` method:</span></span>

* <span data-ttu-id="5fb02-180">Aceita uma cadeia de caracteres de caminho de arquivo, que pode usar [padrões glob](#glob-patterns) para especificar vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="5fb02-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="5fb02-181">Retorna um <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="5fb02-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="5fb02-182">O token de alteração resultante expõe:</span><span class="sxs-lookup"><span data-stu-id="5fb02-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="5fb02-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Uma propriedade que pode ser inspecionada para determinar se ocorreu uma alteração.</span><span class="sxs-lookup"><span data-stu-id="5fb02-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="5fb02-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Chamado quando são detectadas alterações na cadeia de caracteres do caminho especificado.</span><span class="sxs-lookup"><span data-stu-id="5fb02-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="5fb02-185">Cada token de alteração chama apenas seu retorno de chamada associado em resposta a uma única alteração.</span><span class="sxs-lookup"><span data-stu-id="5fb02-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="5fb02-186">Para permitir o monitoramento constante, use um <xref:System.Threading.Tasks.TaskCompletionSource`1> (mostrado abaixo) ou recrie instâncias de `IChangeToken` em resposta a alterações.</span><span class="sxs-lookup"><span data-stu-id="5fb02-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="5fb02-187">O aplicativo de exemplo *WatchConsole* grava uma mensagem sempre que um arquivo *. txt* no diretório *textfiles* é modificado:</span><span class="sxs-lookup"><span data-stu-id="5fb02-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="5fb02-188">Alguns sistemas de arquivos, como contêineres do Docker e compartilhamentos de rede, podem não enviar notificações de alteração de forma confiável.</span><span class="sxs-lookup"><span data-stu-id="5fb02-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="5fb02-189">Defina a variável de ambiente `DOTNET_USE_POLLING_FILE_WATCHER` como `1` ou `true` para sondar o sistema de arquivos a cada quatro segundos em relação a alterações.</span><span class="sxs-lookup"><span data-stu-id="5fb02-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="5fb02-190">Padrões glob</span><span class="sxs-lookup"><span data-stu-id="5fb02-190">Glob patterns</span></span>

<span data-ttu-id="5fb02-191">Os caminhos do sistema de arquivos usam padrões curinga chamados *padrões glob (ou globbing)*.</span><span class="sxs-lookup"><span data-stu-id="5fb02-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="5fb02-192">Especifique grupos de arquivos com esses padrões.</span><span class="sxs-lookup"><span data-stu-id="5fb02-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="5fb02-193">Os dois caracteres curinga são `*` e `**`:</span><span class="sxs-lookup"><span data-stu-id="5fb02-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="5fb02-194">Corresponde a qualquer coisa no nível da pasta atual, qualquer nome de arquivo ou qualquer extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="5fb02-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="5fb02-195">As correspondências são terminadas pelos caracteres `/` e `.` no caminho do arquivo.</span><span class="sxs-lookup"><span data-stu-id="5fb02-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="5fb02-196">Coincide a qualquer coisa em vários níveis de diretório.</span><span class="sxs-lookup"><span data-stu-id="5fb02-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="5fb02-197">Pode ser usada para fazer a correspondência recursiva com vários arquivos em uma hierarquia de diretórios.</span><span class="sxs-lookup"><span data-stu-id="5fb02-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="5fb02-198">A tabela a seguir fornece exemplos comuns de padrões de glob.</span><span class="sxs-lookup"><span data-stu-id="5fb02-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="5fb02-199">Padrão</span><span class="sxs-lookup"><span data-stu-id="5fb02-199">Pattern</span></span>  |<span data-ttu-id="5fb02-200">Descrição</span><span class="sxs-lookup"><span data-stu-id="5fb02-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="5fb02-201">Corresponde a um arquivo específico em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="5fb02-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="5fb02-202">Corresponde a todos os arquivos com a extensão *.txt* em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="5fb02-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="5fb02-203">Corresponde a todos os *appsettings.jsem* arquivos em diretórios exatamente um nível abaixo da pasta do *diretório* .</span><span class="sxs-lookup"><span data-stu-id="5fb02-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="5fb02-204">Corresponde a todos os arquivos com uma extensão *. txt* encontrado em qualquer lugar na pasta do *diretório* .</span><span class="sxs-lookup"><span data-stu-id="5fb02-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5fb02-205">O ASP.NET Core abstrai o acesso ao sistema de arquivos por meio do uso de provedores de arquivos.</span><span class="sxs-lookup"><span data-stu-id="5fb02-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="5fb02-206">Os provedores de arquivos são usados ​​em toda a estrutura do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5fb02-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="5fb02-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> expõe a raiz do [conteúdo](xref:fundamentals/index#content-root) do aplicativo e a [raiz da Web](xref:fundamentals/index#web-root) como `IFileProvider` tipos.</span><span class="sxs-lookup"><span data-stu-id="5fb02-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="5fb02-208">O [middleware de arquivos estáticos](xref:fundamentals/static-files) usa provedores de arquivos para localizar arquivos estáticos.</span><span class="sxs-lookup"><span data-stu-id="5fb02-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="5fb02-209">[Razor](xref:mvc/views/razor) usa provedores de arquivos para localizar páginas e exibições.</span><span class="sxs-lookup"><span data-stu-id="5fb02-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="5fb02-210">As ferramentas do .NET Core usam provedores de arquivos e padrões glob para especificar quais arquivos devem ser publicados.</span><span class="sxs-lookup"><span data-stu-id="5fb02-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="5fb02-211">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5fb02-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="5fb02-212">Interfaces de provedor de arquivo</span><span class="sxs-lookup"><span data-stu-id="5fb02-212">File Provider interfaces</span></span>

<span data-ttu-id="5fb02-213">A interface principal é <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="5fb02-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="5fb02-214">`IFileProvider` expõe métodos para:</span><span class="sxs-lookup"><span data-stu-id="5fb02-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="5fb02-215">Obter informações do arquivo (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="5fb02-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="5fb02-216">Obter informações do diretório (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="5fb02-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="5fb02-217">Configurar notificações de alteração (usando um <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="5fb02-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="5fb02-218">`IFileInfo` fornece métodos e propriedades para trabalhar com arquivos:</span><span class="sxs-lookup"><span data-stu-id="5fb02-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="5fb02-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (em bytes)</span><span class="sxs-lookup"><span data-stu-id="5fb02-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="5fb02-220">Data de <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified></span><span class="sxs-lookup"><span data-stu-id="5fb02-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="5fb02-221">Você pode ler o arquivo usando o método [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).</span><span class="sxs-lookup"><span data-stu-id="5fb02-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="5fb02-222">O aplicativo de amostra demonstra como configurar um provedor de arquivos em `Startup.ConfigureServices` para uso em todo o aplicativo por meio da [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5fb02-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="5fb02-223">Implementações do provedor de arquivos</span><span class="sxs-lookup"><span data-stu-id="5fb02-223">File Provider implementations</span></span>

<span data-ttu-id="5fb02-224">Três implementações de `IFileProvider` estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="5fb02-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="5fb02-225">Implementação</span><span class="sxs-lookup"><span data-stu-id="5fb02-225">Implementation</span></span> | <span data-ttu-id="5fb02-226">Descrição</span><span class="sxs-lookup"><span data-stu-id="5fb02-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="5fb02-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="5fb02-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="5fb02-228">O provedor físico é usado para acessar os arquivos físicos do sistema.</span><span class="sxs-lookup"><span data-stu-id="5fb02-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="5fb02-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="5fb02-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="5fb02-230">O provedor inserido de manifesto é usado para acessar arquivos incorporados em assemblies.</span><span class="sxs-lookup"><span data-stu-id="5fb02-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="5fb02-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="5fb02-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="5fb02-232">O provedor composto é usado para fornecer acesso combinado a arquivos e diretórios de um ou mais provedores.</span><span class="sxs-lookup"><span data-stu-id="5fb02-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="5fb02-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="5fb02-233">PhysicalFileProvider</span></span>

<span data-ttu-id="5fb02-234">O <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fornece acesso ao sistema de arquivos físico.</span><span class="sxs-lookup"><span data-stu-id="5fb02-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="5fb02-235">O `PhysicalFileProvider` usa o tipo <xref:System.IO.File?displayProperty=fullName> (para o provedor físico) e define o escopo de todos os caminhos para um diretório e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="5fb02-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="5fb02-236">Esse escopo impede o acesso ao sistema de arquivos fora do diretório especificado e seus filhos.</span><span class="sxs-lookup"><span data-stu-id="5fb02-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="5fb02-237">O cenário mais comum para criar e usar um `PhysicalFileProvider` é solicitar um `IFileProvider` em um construtor por meio de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5fb02-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5fb02-238">Ao criar uma instância para esse provedor, um caminho de diretório é necessário e serve como o caminho base para todas as solicitações feitas usando o provedor.</span><span class="sxs-lookup"><span data-stu-id="5fb02-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="5fb02-239">O código a seguir mostra como criar um `PhysicalFileProvider` e usá-lo para obter o conteúdo do diretório e as informações do arquivo:</span><span class="sxs-lookup"><span data-stu-id="5fb02-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="5fb02-240">Tipos no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="5fb02-240">Types in the preceding example:</span></span>

* <span data-ttu-id="5fb02-241">`provider` é um `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="5fb02-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="5fb02-242">`contents` é um `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="5fb02-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="5fb02-243">`fileInfo` é um `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="5fb02-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="5fb02-244">O provedor de arquivos pode ser usado para percorrer o diretório especificado por `applicationRoot` ou chamar `GetFileInfo` para obter as informações de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="5fb02-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="5fb02-245">O provedor de arquivos não tem acesso fora do diretório `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="5fb02-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="5fb02-246">O aplicativo de amostra cria o provedor na classe `Startup.ConfigureServices` do aplicativo usando [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="5fb02-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="5fb02-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="5fb02-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="5fb02-248">O <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> é usado para acessar arquivos inseridos em assemblies.</span><span class="sxs-lookup"><span data-stu-id="5fb02-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="5fb02-249">O `ManifestEmbeddedFileProvider` usa um manifesto compilado no assembly para reconstruir os caminhos originais dos arquivos inseridos.</span><span class="sxs-lookup"><span data-stu-id="5fb02-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="5fb02-250">Para gerar um manifesto dos arquivos inseridos, defina a propriedade `<GenerateEmbeddedFilesManifest>` como `true`.</span><span class="sxs-lookup"><span data-stu-id="5fb02-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="5fb02-251">Especifique os arquivos a serem inseridos com [ &lt; EmbeddedResource &gt; ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="5fb02-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-xml[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="5fb02-252">Use [padrões glob](#glob-patterns) para especificar um ou mais arquivos a serem inseridos no assembly.</span><span class="sxs-lookup"><span data-stu-id="5fb02-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="5fb02-253">O aplicativo de amostra cria um `ManifestEmbeddedFileProvider` e passa o assembly atualmente em execução para seu construtor.</span><span class="sxs-lookup"><span data-stu-id="5fb02-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="5fb02-254">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="5fb02-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="5fb02-255">Sobrecargas adicionais permitem:</span><span class="sxs-lookup"><span data-stu-id="5fb02-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="5fb02-256">Especificar um caminho de arquivo relativo.</span><span class="sxs-lookup"><span data-stu-id="5fb02-256">Specify a relative file path.</span></span>
* <span data-ttu-id="5fb02-257">Delimitar os arquivos segundo a data da última modificação.</span><span class="sxs-lookup"><span data-stu-id="5fb02-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="5fb02-258">Nomear o recurso inserido que contém o manifesto do arquivo inserido.</span><span class="sxs-lookup"><span data-stu-id="5fb02-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="5fb02-259">Sobrecarga</span><span class="sxs-lookup"><span data-stu-id="5fb02-259">Overload</span></span> | <span data-ttu-id="5fb02-260">Descrição</span><span class="sxs-lookup"><span data-stu-id="5fb02-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="5fb02-261">Aceita um parâmetro de caminho relativo `root` opcional.</span><span class="sxs-lookup"><span data-stu-id="5fb02-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="5fb02-262">Especifique o `root` para definir o escopo das chamadas de <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> para esses recursos no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="5fb02-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="5fb02-263">Aceita um parâmetro de caminho relativo `root` opcional e um parâmetro de data `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="5fb02-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="5fb02-264">A data `lastModified` tem como escopo a data da última modificação das instâncias de <xref:Microsoft.Extensions.FileProviders.IFileInfo> retornadas por <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="5fb02-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="5fb02-265">Aceita um caminho relativo `root` opcional, data de `lastModified` e parâmetros `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="5fb02-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="5fb02-266">O `manifestName` representa o nome do recurso inserido que contém o manifesto.</span><span class="sxs-lookup"><span data-stu-id="5fb02-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="5fb02-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="5fb02-267">CompositeFileProvider</span></span>

<span data-ttu-id="5fb02-268">O <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combina instâncias de `IFileProvider`, expondo uma interface única para trabalhar com arquivos de vários provedores.</span><span class="sxs-lookup"><span data-stu-id="5fb02-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="5fb02-269">Ao criar o `CompositeFileProvider`, passe uma ou mais instâncias de `IFileProvider` para o construtor.</span><span class="sxs-lookup"><span data-stu-id="5fb02-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="5fb02-270">No aplicativo de amostra, um `PhysicalFileProvider` e um `ManifestEmbeddedFileProvider` fornecem arquivos para um `CompositeFileProvider` registrado no contêiner de serviço do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="5fb02-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="5fb02-271">Monitorar as alterações</span><span class="sxs-lookup"><span data-stu-id="5fb02-271">Watch for changes</span></span>

<span data-ttu-id="5fb02-272">O método [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) proporciona um cenário para monitorar um ou mais arquivos ou diretórios quanto a alterações.</span><span class="sxs-lookup"><span data-stu-id="5fb02-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="5fb02-273">`Watch` aceita uma cadeia de caracteres de caminho, que pode usar [padrões glob](#glob-patterns) para especificar vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="5fb02-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="5fb02-274">`Watch` retorna um <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="5fb02-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="5fb02-275">O token de alteração expõe:</span><span class="sxs-lookup"><span data-stu-id="5fb02-275">The change token exposes:</span></span>

* <span data-ttu-id="5fb02-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Uma propriedade que pode ser inspecionada para determinar se ocorreu uma alteração.</span><span class="sxs-lookup"><span data-stu-id="5fb02-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="5fb02-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Chamado quando são detectadas alterações na cadeia de caracteres do caminho especificado.</span><span class="sxs-lookup"><span data-stu-id="5fb02-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="5fb02-278">Cada token de alteração chama apenas seu retorno de chamada associado em resposta a uma única alteração.</span><span class="sxs-lookup"><span data-stu-id="5fb02-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="5fb02-279">Para permitir o monitoramento constante, use um <xref:System.Threading.Tasks.TaskCompletionSource`1> (mostrado abaixo) ou recrie instâncias de `IChangeToken` em resposta a alterações.</span><span class="sxs-lookup"><span data-stu-id="5fb02-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="5fb02-280">No aplicativo de amostra, o aplicativo de console *WatchConsole* é configurado para exibir uma mensagem sempre que um arquivo de texto é modificado:</span><span class="sxs-lookup"><span data-stu-id="5fb02-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="5fb02-281">Alguns sistemas de arquivos, como contêineres do Docker e compartilhamentos de rede, podem não enviar notificações de alteração de forma confiável.</span><span class="sxs-lookup"><span data-stu-id="5fb02-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="5fb02-282">Defina a variável de ambiente `DOTNET_USE_POLLING_FILE_WATCHER` como `1` ou `true` para sondar o sistema de arquivos a cada quatro segundos em relação a alterações.</span><span class="sxs-lookup"><span data-stu-id="5fb02-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="5fb02-283">Padrões glob</span><span class="sxs-lookup"><span data-stu-id="5fb02-283">Glob patterns</span></span>

<span data-ttu-id="5fb02-284">Os caminhos do sistema de arquivos usam padrões curinga chamados *padrões glob (ou globbing)*.</span><span class="sxs-lookup"><span data-stu-id="5fb02-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="5fb02-285">Especifique grupos de arquivos com esses padrões.</span><span class="sxs-lookup"><span data-stu-id="5fb02-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="5fb02-286">Os dois caracteres curinga são `*` e `**`:</span><span class="sxs-lookup"><span data-stu-id="5fb02-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="5fb02-287">Corresponde a qualquer coisa no nível da pasta atual, qualquer nome de arquivo ou qualquer extensão de arquivo.</span><span class="sxs-lookup"><span data-stu-id="5fb02-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="5fb02-288">As correspondências são terminadas pelos caracteres `/` e `.` no caminho do arquivo.</span><span class="sxs-lookup"><span data-stu-id="5fb02-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="5fb02-289">Coincide a qualquer coisa em vários níveis de diretório.</span><span class="sxs-lookup"><span data-stu-id="5fb02-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="5fb02-290">Pode ser usada para fazer a correspondência recursiva com vários arquivos em uma hierarquia de diretórios.</span><span class="sxs-lookup"><span data-stu-id="5fb02-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="5fb02-291">**Exemplos de padrão glob**</span><span class="sxs-lookup"><span data-stu-id="5fb02-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="5fb02-292">Corresponde a um arquivo específico em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="5fb02-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="5fb02-293">Corresponde a todos os arquivos com a extensão *.txt* em um diretório específico.</span><span class="sxs-lookup"><span data-stu-id="5fb02-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="5fb02-294">Corresponde a todos os arquivos `appsettings.json` em diretórios que estão exatamente um nível abaixo da pasta *diretório*.</span><span class="sxs-lookup"><span data-stu-id="5fb02-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="5fb02-295">Corresponde a todos os arquivos com a extensão *.txt* encontrados em qualquer lugar abaixo da pasta *diretório*.</span><span class="sxs-lookup"><span data-stu-id="5fb02-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
