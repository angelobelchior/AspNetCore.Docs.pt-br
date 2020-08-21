---
title: Carregar arquivos no ASP.NET Core
author: rick-anderson
description: Como usar o model binding e o streaming para carregar arquivos no ASP.NET Core MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
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
uid: mvc/models/file-uploads
ms.openlocfilehash: 6ff78b26e8e2363cf6c54ebb2a392f390fb2995c
ms.sourcegitcommit: cd412a44f26cb416ceb348fc0a1ccc9a6e9ca73e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88720273"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="444a8-103">Carregar arquivos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="444a8-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="444a8-104">Por [Steve Smith](https://ardalis.com/) e [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="444a8-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="444a8-105">O ASP.NET Core dá suporte ao carregamento de um ou mais arquivos usando a associação de modelo em buffer para arquivos menores e streaming sem buffer para arquivos maiores.</span><span class="sxs-lookup"><span data-stu-id="444a8-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="444a8-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="444a8-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="444a8-107">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="444a8-107">Security considerations</span></span>

<span data-ttu-id="444a8-108">Tome cuidado ao fornecer aos usuários a capacidade de carregar arquivos em um servidor.</span><span class="sxs-lookup"><span data-stu-id="444a8-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="444a8-109">Os invasores podem tentar:</span><span class="sxs-lookup"><span data-stu-id="444a8-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="444a8-110">Executar ataques [de negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="444a8-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="444a8-111">Carregar vírus ou malware.</span><span class="sxs-lookup"><span data-stu-id="444a8-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="444a8-112">Comprometa redes e servidores de outras maneiras.</span><span class="sxs-lookup"><span data-stu-id="444a8-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="444a8-113">As etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:</span><span class="sxs-lookup"><span data-stu-id="444a8-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="444a8-114">Carregue arquivos em uma área de carregamento de arquivo dedicada, preferencialmente para uma unidade que não seja do sistema.</span><span class="sxs-lookup"><span data-stu-id="444a8-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="444a8-115">Um local dedicado torna mais fácil impor restrições de segurança em arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="444a8-116">Desabilite as permissões de execução no local de carregamento do arquivo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="444a8-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="444a8-117">**Não** persiste arquivos carregados na mesma árvore de diretório que o aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="444a8-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="444a8-118">Use um nome de arquivo seguro determinado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="444a8-119">Não use um nome de arquivo fornecido pelo usuário ou o nome de arquivo não confiável do arquivo carregado. &dagger; O HTML codifica o nome de arquivo não confiável ao exibi-lo.</span><span class="sxs-lookup"><span data-stu-id="444a8-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="444a8-120">Por exemplo, registrar em log o nome do arquivo ou exibindo na interface do usuário (a Razor saída de código HTML automaticamente).</span><span class="sxs-lookup"><span data-stu-id="444a8-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="444a8-121">Permitir somente extensões de arquivo aprovadas para a especificação de design do aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="444a8-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="444a8-122">Verifique se as verificações do lado do cliente são executadas no servidor. &dagger; As verificações no lado do cliente são fáceis de evitar.</span><span class="sxs-lookup"><span data-stu-id="444a8-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="444a8-123">Verifique o tamanho de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="444a8-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="444a8-124">Defina um limite de tamanho máximo para evitar carregamentos grandes.&dagger;</span><span class="sxs-lookup"><span data-stu-id="444a8-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="444a8-125">Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="444a8-126">**Execute um scanner de vírus/malware no conteúdo carregado antes de o arquivo ser armazenado.**</span><span class="sxs-lookup"><span data-stu-id="444a8-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="444a8-127">&dagger;O aplicativo de exemplo demonstra uma abordagem que atende aos critérios.</span><span class="sxs-lookup"><span data-stu-id="444a8-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="444a8-128">Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:</span><span class="sxs-lookup"><span data-stu-id="444a8-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="444a8-129">Assuma completamente o controle de um sistema.</span><span class="sxs-lookup"><span data-stu-id="444a8-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="444a8-130">Sobrecarregar um sistema com o resultado que o sistema falha.</span><span class="sxs-lookup"><span data-stu-id="444a8-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="444a8-131">Comprometer dados do sistema ou de usuários.</span><span class="sxs-lookup"><span data-stu-id="444a8-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="444a8-132">Aplique graffiti a uma interface do usuário pública.</span><span class="sxs-lookup"><span data-stu-id="444a8-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="444a8-133">Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="444a8-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="444a8-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="444a8-134">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="444a8-135">Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários</span><span class="sxs-lookup"><span data-stu-id="444a8-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="444a8-136">Para obter mais informações sobre como implementar medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [validação](#validation) .</span><span class="sxs-lookup"><span data-stu-id="444a8-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="444a8-137">Cenários de armazenamento</span><span class="sxs-lookup"><span data-stu-id="444a8-137">Storage scenarios</span></span>

<span data-ttu-id="444a8-138">As opções de armazenamento comuns para arquivos incluem:</span><span class="sxs-lookup"><span data-stu-id="444a8-138">Common storage options for files include:</span></span>

* <span data-ttu-id="444a8-139">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="444a8-139">Database</span></span>

  * <span data-ttu-id="444a8-140">Para carregamentos de arquivos pequenos, um banco de dados geralmente é mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).</span><span class="sxs-lookup"><span data-stu-id="444a8-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="444a8-141">Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, pois a recuperação de um registro de banco de dados para o usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de Avatar).</span><span class="sxs-lookup"><span data-stu-id="444a8-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="444a8-142">Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dado.</span><span class="sxs-lookup"><span data-stu-id="444a8-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="444a8-143">Armazenamento físico (sistema de arquivos ou compartilhamento de rede)</span><span class="sxs-lookup"><span data-stu-id="444a8-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="444a8-144">Para carregamentos de arquivos grandes:</span><span class="sxs-lookup"><span data-stu-id="444a8-144">For large file uploads:</span></span>
    * <span data-ttu-id="444a8-145">Os limites de banco de dados podem restringir o tamanho do carregamento.</span><span class="sxs-lookup"><span data-stu-id="444a8-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="444a8-146">O armazenamento físico é geralmente menos econômico do que o armazenamento em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="444a8-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="444a8-147">O armazenamento físico é potencialmente mais barato do que usar um serviço de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="444a8-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="444a8-148">O processo do aplicativo deve ter permissões de leitura e gravação no local de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="444a8-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="444a8-149">**Nunca conceda a permissão EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="444a8-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="444a8-150">Serviço de armazenamento de dados (por exemplo, [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="444a8-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="444a8-151">Os serviços geralmente oferecem escalabilidade e resiliência aprimoradas sobre soluções locais que geralmente estão sujeitas a pontos únicos de falha.</span><span class="sxs-lookup"><span data-stu-id="444a8-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="444a8-152">Os serviços são um custo potencialmente menor em cenários de infraestrutura de armazenamento grandes.</span><span class="sxs-lookup"><span data-stu-id="444a8-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="444a8-153">Para obter mais informações, consulte [início rápido: usar o .net para criar um blob no armazenamento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="444a8-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="444a8-154">Cenários de upload de arquivo</span><span class="sxs-lookup"><span data-stu-id="444a8-154">File upload scenarios</span></span>

<span data-ttu-id="444a8-155">Duas abordagens gerais para carregar arquivos são buffer e streaming.</span><span class="sxs-lookup"><span data-stu-id="444a8-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="444a8-156">**de resposta**</span><span class="sxs-lookup"><span data-stu-id="444a8-156">**Buffering**</span></span>

<span data-ttu-id="444a8-157">O arquivo inteiro é lido em um <xref:Microsoft.AspNetCore.Http.IFormFile> , que é uma representação em C# do arquivo usado para processar ou salvar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="444a8-158">Os recursos (disco, memória) usados por carregamentos de arquivos dependem do número e do tamanho de carregamentos de arquivos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="444a8-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="444a8-159">Se um aplicativo tentar armazenar em buffer um número excessivo de carregamentos, o site falhará quando ficar sem memória ou espaço em disco.</span><span class="sxs-lookup"><span data-stu-id="444a8-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="444a8-160">Se o tamanho ou a frequência dos carregamentos de arquivos estiver esgotando os recursos do aplicativo, use o streaming.</span><span class="sxs-lookup"><span data-stu-id="444a8-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="444a8-161">Qualquer arquivo em buffer único que exceda 64 KB é movido da memória para um arquivo temporário no disco.</span><span class="sxs-lookup"><span data-stu-id="444a8-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="444a8-162">O armazenamento em buffer de arquivos pequenos é abordado nas seguintes seções deste tópico:</span><span class="sxs-lookup"><span data-stu-id="444a8-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="444a8-163">Armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="444a8-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="444a8-164">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="444a8-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="444a8-165">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="444a8-165">**Streaming**</span></span>

<span data-ttu-id="444a8-166">O arquivo é recebido de uma solicitação de várias partes e processada ou salva diretamente pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="444a8-167">O streaming não melhora significativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="444a8-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="444a8-168">O streaming reduz as demandas de memória ou espaço em disco ao carregar arquivos.</span><span class="sxs-lookup"><span data-stu-id="444a8-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="444a8-169">O streaming de arquivos grandes é abordado na seção [carregar arquivos grandes com streaming](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="444a8-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="444a8-170">Carregar arquivos pequenos com associação de modelo em buffer para armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="444a8-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="444a8-171">Para carregar arquivos pequenos, use um formulário com diversas partes ou Construa uma solicitação POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="444a8-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="444a8-172">O exemplo a seguir demonstra o uso de um Razor formulário de páginas para carregar um único arquivo (*pages/BufferedSingleFileUploadPhysical. cshtml* no aplicativo de exemplo):</span><span class="sxs-lookup"><span data-stu-id="444a8-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="444a8-173">O exemplo a seguir é análogo ao exemplo anterior, exceto pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="444a8-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="444a8-174">O JavaScript ([buscar API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) é usado para enviar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="444a8-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="444a8-175">Não há validação.</span><span class="sxs-lookup"><span data-stu-id="444a8-175">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="444a8-176">Para executar a POSTAgem de formulário no JavaScript para clientes que [não dão suporte à API de busca](https://caniuse.com/#feat=fetch), use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="444a8-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="444a8-177">Use um polifill de busca (por exemplo, [janela. FETCH retroativo (github/FETCH)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="444a8-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="444a8-178">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="444a8-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="444a8-179">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="444a8-179">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="444a8-180">Para dar suporte a uploads de arquivo, os formulários HTML devem especificar um tipo de codificação ( `enctype` ) de `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="444a8-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="444a8-181">Para um `files` elemento input oferecer suporte ao carregamento de vários arquivos, forneça o `multiple` atributo no `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="444a8-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="444a8-182">Os arquivos individuais carregados no servidor podem ser acessados por meio de [Associação de modelo](xref:mvc/models/model-binding) usando <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="444a8-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="444a8-183">O aplicativo de exemplo demonstra vários uploads de arquivo em buffer para cenários de armazenamento físico e banco de dados.</span><span class="sxs-lookup"><span data-stu-id="444a8-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="444a8-184">**Não** use a `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> outros para exibição e registro em log.</span><span class="sxs-lookup"><span data-stu-id="444a8-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="444a8-185">Ao exibir ou registrar em log, o HTML codifica o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="444a8-186">Um invasor pode fornecer um nome de arquivo mal-intencionado, incluindo caminhos completos ou caminhos relativos.</span><span class="sxs-lookup"><span data-stu-id="444a8-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="444a8-187">Os aplicativos devem:</span><span class="sxs-lookup"><span data-stu-id="444a8-187">Applications should:</span></span>
>
> * <span data-ttu-id="444a8-188">Remova o caminho do nome de arquivo fornecido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="444a8-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="444a8-189">Salve o nome de arquivo com codificação em HTML e removido por caminho para a interface do usuário ou log.</span><span class="sxs-lookup"><span data-stu-id="444a8-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="444a8-190">Gere um novo nome de arquivo aleatório para armazenamento.</span><span class="sxs-lookup"><span data-stu-id="444a8-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="444a8-191">O código a seguir remove o caminho do nome do arquivo:</span><span class="sxs-lookup"><span data-stu-id="444a8-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="444a8-192">Os exemplos fornecidos até o momento não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="444a8-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="444a8-193">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="444a8-194">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="444a8-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="444a8-195">Validação</span><span class="sxs-lookup"><span data-stu-id="444a8-195">Validation</span></span>](#validation)

<span data-ttu-id="444a8-196">Ao carregar arquivos usando a associação de modelo e <xref:Microsoft.AspNetCore.Http.IFormFile> o método de ação pode aceitar:</span><span class="sxs-lookup"><span data-stu-id="444a8-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="444a8-197">Um único <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="444a8-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="444a8-198">Qualquer uma das seguintes coleções que representam vários arquivos:</span><span class="sxs-lookup"><span data-stu-id="444a8-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="444a8-199">[Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="444a8-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="444a8-200">A Associação corresponde aos arquivos de formulário por nome.</span><span class="sxs-lookup"><span data-stu-id="444a8-200">Binding matches form files by name.</span></span> <span data-ttu-id="444a8-201">Por exemplo, o `name` valor HTML in `<input type="file" name="formFile">` deve corresponder ao parâmetro/Propriedade do C# associado ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="444a8-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="444a8-202">Para obter mais informações, consulte o [valor do atributo Match Name para o nome do parâmetro da seção do método post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="444a8-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="444a8-203">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-203">The following example:</span></span>

* <span data-ttu-id="444a8-204">Executa um loop em um ou mais arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="444a8-205">Usa [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="444a8-206">Salva os arquivos no sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="444a8-207">Retorna o número total e o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-207">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="444a8-208">Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho.</span><span class="sxs-lookup"><span data-stu-id="444a8-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="444a8-209">No exemplo a seguir, o caminho é obtido da configuração:</span><span class="sxs-lookup"><span data-stu-id="444a8-209">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="444a8-210">O caminho passado para o <xref:System.IO.FileStream> *deve* incluir o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="444a8-211">Se o nome do arquivo não for fornecido, um <xref:System.UnauthorizedAccessException> será lançado no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="444a8-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="444a8-212">Os arquivos carregados usando a <xref:Microsoft.AspNetCore.Http.IFormFile> técnica são armazenados em buffer na memória ou no disco no servidor antes do processamento.</span><span class="sxs-lookup"><span data-stu-id="444a8-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="444a8-213">Dentro do método de ação, o <xref:Microsoft.AspNetCore.Http.IFormFile> conteúdo é acessível como um <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="444a8-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="444a8-214">Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como o [armazenamento de BLOBs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="444a8-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="444a8-215">Para outro exemplo que executa um loop sobre vários arquivos para carregar e usa nomes de arquivo seguros, consulte *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="444a8-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="444a8-216">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) gera um <xref:System.IO.IOException> se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores.</span><span class="sxs-lookup"><span data-stu-id="444a8-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="444a8-217">O limite de 65.535 arquivos é um limite por servidor.</span><span class="sxs-lookup"><span data-stu-id="444a8-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="444a8-218">Para obter mais informações sobre esse limite no sistema operacional Windows, consulte os comentários nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="444a8-219">Função GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="444a8-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="444a8-220">Carregar arquivos pequenos com associação de modelo em buffer para um banco de dados</span><span class="sxs-lookup"><span data-stu-id="444a8-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="444a8-221">Para armazenar dados de arquivo binário em um banco de dado usando [Entity Framework](/ef/core/index), defina uma <xref:System.Byte> propriedade de matriz na entidade:</span><span class="sxs-lookup"><span data-stu-id="444a8-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="444a8-222">Especifique uma propriedade de modelo de página para a classe que inclui um <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="444a8-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="444a8-223"><xref:Microsoft.AspNetCore.Http.IFormFile> pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="444a8-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="444a8-224">O exemplo anterior usa uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="444a8-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="444a8-225">O `FileUpload` é usado no Razor formulário páginas:</span><span class="sxs-lookup"><span data-stu-id="444a8-225">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="444a8-226">Quando o formulário for Postado no servidor, copie o <xref:Microsoft.AspNetCore.Http.IFormFile> para um fluxo e salve-o como uma matriz de bytes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="444a8-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="444a8-227">No exemplo a seguir, `_dbContext` armazena o contexto do banco de dados do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="444a8-227">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="444a8-228">O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="444a8-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="444a8-229">*Páginas/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="444a8-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="444a8-230">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="444a8-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="444a8-231">Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="444a8-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="444a8-232">Não confie ou confie na `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação.</span><span class="sxs-lookup"><span data-stu-id="444a8-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="444a8-233">A `FileName` propriedade só deve ser usada para fins de exibição e somente após a codificação HTML.</span><span class="sxs-lookup"><span data-stu-id="444a8-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="444a8-234">Os exemplos fornecidos não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="444a8-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="444a8-235">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="444a8-236">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="444a8-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="444a8-237">Validação</span><span class="sxs-lookup"><span data-stu-id="444a8-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="444a8-238">Carregar arquivos grandes com streaming</span><span class="sxs-lookup"><span data-stu-id="444a8-238">Upload large files with streaming</span></span>

<span data-ttu-id="444a8-239">O exemplo a seguir demonstra como usar o JavaScript para transmitir um arquivo a uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="444a8-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="444a8-240">O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="444a8-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="444a8-241">Como o método de ação processa diretamente os dados carregados, a associação de modelo de formulário é desabilitada por outro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="444a8-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="444a8-242">Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="444a8-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="444a8-243">Depois que as seções com várias partes forem lidas, a ação executará sua própria associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="444a8-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="444a8-244">A resposta de página inicial carrega o formulário e salva um token de antifalsificação em um cookie (por meio do `GenerateAntiforgeryTokenCookieAttribute` atributo).</span><span class="sxs-lookup"><span data-stu-id="444a8-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="444a8-245">O atributo usa o [suporte de antifalsificação](xref:security/anti-request-forgery) interno do ASP.NET Core para definir um cookie com um token de solicitação:</span><span class="sxs-lookup"><span data-stu-id="444a8-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="444a8-246">O `DisableFormValueModelBindingAttribute` é usado para desabilitar a associação de modelo:</span><span class="sxs-lookup"><span data-stu-id="444a8-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="444a8-247">No aplicativo de exemplo, `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` são aplicados como filtros aos modelos de aplicativo de página do `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` no `Startup.ConfigureServices` usando convenções de [ Razor páginas](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="444a8-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="444a8-248">Como a associação de modelo não lê o formulário, os parâmetros que estão associados do formulário não são associados (a consulta, a rota e o cabeçalho continuam a funcionar).</span><span class="sxs-lookup"><span data-stu-id="444a8-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="444a8-249">O método de ação funciona diretamente com a `Request` propriedade.</span><span class="sxs-lookup"><span data-stu-id="444a8-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="444a8-250">Um `MultipartReader` é usado para ler cada seção.</span><span class="sxs-lookup"><span data-stu-id="444a8-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="444a8-251">Os dados de chave/valor são armazenados em um `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="444a8-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="444a8-252">Depois que as seções com várias partes forem lidas, o conteúdo do `KeyValueAccumulator` será usado para associar os dados do formulário a um tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="444a8-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="444a8-253">O `StreamingController.UploadDatabase` método completo para streaming para um banco de dados com EF Core:</span><span class="sxs-lookup"><span data-stu-id="444a8-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="444a8-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="444a8-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="444a8-255">O `StreamingController.UploadPhysical` método completo para streaming para um local físico:</span><span class="sxs-lookup"><span data-stu-id="444a8-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="444a8-256">No aplicativo de exemplo, as verificações de validação são tratadas pelo `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="444a8-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="444a8-257">Validação</span><span class="sxs-lookup"><span data-stu-id="444a8-257">Validation</span></span>

<span data-ttu-id="444a8-258">A classe do aplicativo de exemplo `FileHelpers` demonstra várias verificações para <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivos em buffer e em fluxo.</span><span class="sxs-lookup"><span data-stu-id="444a8-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="444a8-259">Para processar <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivo em buffer no aplicativo de exemplo, consulte o `ProcessFormFile` método no arquivo *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="444a8-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="444a8-260">Para o processamento de arquivos transmitidos, consulte o `ProcessStreamedFile` método no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="444a8-261">Os métodos de processamento de validação demonstrados no aplicativo de exemplo não verificam o conteúdo dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="444a8-262">Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para os usuários ou outros sistemas.</span><span class="sxs-lookup"><span data-stu-id="444a8-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="444a8-263">Embora o tópico exemplo forneça um exemplo de trabalho de técnicas de validação, não implemente a `FileHelpers` classe em um aplicativo de produção, a menos que você:</span><span class="sxs-lookup"><span data-stu-id="444a8-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="444a8-264">Entenda totalmente a implementação.</span><span class="sxs-lookup"><span data-stu-id="444a8-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="444a8-265">Modifique a implementação conforme apropriado para o ambiente e as especificações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="444a8-266">**Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**</span><span class="sxs-lookup"><span data-stu-id="444a8-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="444a8-267">Validação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="444a8-267">Content validation</span></span>

<span data-ttu-id="444a8-268">**Use uma API de verificação de vírus/malware de terceiros no conteúdo carregado.**</span><span class="sxs-lookup"><span data-stu-id="444a8-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="444a8-269">A verificação de arquivos exige muitos recursos do servidor em cenários de alto volume.</span><span class="sxs-lookup"><span data-stu-id="444a8-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="444a8-270">Se o desempenho do processamento de solicitações for reduzido devido à verificação de arquivo, considere descarregar o trabalho de verificação para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="444a8-271">Normalmente, os arquivos carregados são mantidos em uma área em quarentena até que o verificador de vírus em segundo plano os Verifique.</span><span class="sxs-lookup"><span data-stu-id="444a8-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="444a8-272">Quando um arquivo passa, o arquivo é movido para o local de armazenamento de arquivo normal.</span><span class="sxs-lookup"><span data-stu-id="444a8-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="444a8-273">Essas etapas geralmente são executadas em conjunto com um registro de banco de dados que indica o status de verificação de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="444a8-274">Ao usar essa abordagem, o aplicativo e o servidor de aplicativos permanecem concentrados em responder às solicitações.</span><span class="sxs-lookup"><span data-stu-id="444a8-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="444a8-275">Validação de extensão de arquivo</span><span class="sxs-lookup"><span data-stu-id="444a8-275">File extension validation</span></span>

<span data-ttu-id="444a8-276">A extensão do arquivo carregado deve ser verificada em uma lista de extensões permitidas.</span><span class="sxs-lookup"><span data-stu-id="444a8-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="444a8-277">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="444a8-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="444a8-278">Validação de assinatura de arquivo</span><span class="sxs-lookup"><span data-stu-id="444a8-278">File signature validation</span></span>

<span data-ttu-id="444a8-279">A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="444a8-280">Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="444a8-281">O aplicativo de exemplo verifica as assinaturas de arquivo para alguns tipos de arquivo comuns.</span><span class="sxs-lookup"><span data-stu-id="444a8-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="444a8-282">No exemplo a seguir, a assinatura de arquivo para uma imagem JPEG é verificada em relação ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="444a8-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="444a8-283">Para obter assinaturas de arquivo adicionais, consulte o [banco de dados assinaturas de arquivo](https://www.filesignatures.net/) e especificações de arquivo oficial.</span><span class="sxs-lookup"><span data-stu-id="444a8-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="444a8-284">Segurança de nome de arquivo</span><span class="sxs-lookup"><span data-stu-id="444a8-284">File name security</span></span>

<span data-ttu-id="444a8-285">Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo no armazenamento físico.</span><span class="sxs-lookup"><span data-stu-id="444a8-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="444a8-286">Crie um nome de arquivo seguro para o arquivo usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.</span><span class="sxs-lookup"><span data-stu-id="444a8-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="444a8-287">Razor o HTML automaticamente codifica valores de propriedade para exibição.</span><span class="sxs-lookup"><span data-stu-id="444a8-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="444a8-288">O código a seguir é seguro para usar:</span><span class="sxs-lookup"><span data-stu-id="444a8-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="444a8-289">Fora do Razor , sempre <xref:System.Net.WebUtility.HtmlEncode*> nome do arquivo o conteúdo da solicitação de um usuário.</span><span class="sxs-lookup"><span data-stu-id="444a8-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="444a8-290">Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo será substituído por um arquivo com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="444a8-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="444a8-291">Forneça lógica adicional para atender às especificações do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="444a8-292">Validação de tamanho</span><span class="sxs-lookup"><span data-stu-id="444a8-292">Size validation</span></span>

<span data-ttu-id="444a8-293">Limite o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="444a8-294">No aplicativo de exemplo, o tamanho do arquivo é limitado a 2 MB (indicado em bytes).</span><span class="sxs-lookup"><span data-stu-id="444a8-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="444a8-295">O limite é fornecido por meio da [configuração](xref:fundamentals/configuration/index) do *appsettings.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="444a8-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="444a8-296">O `FileSizeLimit` é injetado em `PageModel` classes:</span><span class="sxs-lookup"><span data-stu-id="444a8-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="444a8-297">Quando um tamanho de arquivo exceder o limite, o arquivo será rejeitado:</span><span class="sxs-lookup"><span data-stu-id="444a8-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="444a8-298">Coincidir valor do atributo de nome com o nome do parâmetro do método POST</span><span class="sxs-lookup"><span data-stu-id="444a8-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="444a8-299">Em não Razor formulários que postam dados de formulário ou usam o JavaScript `FormData` diretamente, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="444a8-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="444a8-300">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-300">In the following example:</span></span>

* <span data-ttu-id="444a8-301">Ao usar um `<input>` elemento, o `name` atributo é definido como o valor `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="444a8-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="444a8-302">Ao usar `FormData` em JavaScript, o nome é definido como o valor `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="444a8-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="444a8-303">Use um nome correspondente para o parâmetro do método C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="444a8-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="444a8-304">Para um Razor método de manipulador de página de páginas chamado `Upload` :</span><span class="sxs-lookup"><span data-stu-id="444a8-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="444a8-305">Para um método de ação do controlador de POSTAgem MVC:</span><span class="sxs-lookup"><span data-stu-id="444a8-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="444a8-306">Configuração de servidor e aplicativo</span><span class="sxs-lookup"><span data-stu-id="444a8-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="444a8-307">Limite de comprimento de corpo com várias partes</span><span class="sxs-lookup"><span data-stu-id="444a8-307">Multipart body length limit</span></span>

<span data-ttu-id="444a8-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> define o limite para o comprimento de cada corpo com diversas partes.</span><span class="sxs-lookup"><span data-stu-id="444a8-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="444a8-309">As seções de formulário que excedem esse limite lançam um <xref:System.IO.InvalidDataException> quando analisado.</span><span class="sxs-lookup"><span data-stu-id="444a8-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="444a8-310">O padrão é 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="444a8-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="444a8-311">Personalize o limite usando a <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> configuração em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="444a8-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="444a8-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> é usado para definir o <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="444a8-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="444a8-313">Em um Razor aplicativo de páginas, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="444a8-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

<span data-ttu-id="444a8-314">Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="444a8-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="444a8-315">Tamanho máximo do corpo de solicitação do Kestrel</span><span class="sxs-lookup"><span data-stu-id="444a8-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="444a8-316">Para aplicativos hospedados pelo Kestrel, o tamanho máximo padrão do corpo da solicitação é 30 milhões bytes, que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="444a8-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="444a8-317">Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="444a8-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="444a8-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="444a8-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="444a8-319">Em um Razor aplicativo de páginas, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="444a8-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

<span data-ttu-id="444a8-320">Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro à classe de manipulador de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="444a8-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="444a8-321">O `RequestSizeLimitAttribute` também pode ser aplicado usando a [`@attribute`](xref:mvc/views/razor#attribute) Razor diretiva:</span><span class="sxs-lookup"><span data-stu-id="444a8-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="444a8-322">Outros limites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="444a8-322">Other Kestrel limits</span></span>

<span data-ttu-id="444a8-323">Outros limites de Kestrel podem ser aplicados para aplicativos hospedados pelo Kestrel:</span><span class="sxs-lookup"><span data-stu-id="444a8-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="444a8-324">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="444a8-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="444a8-325">Taxas de dados de solicitação e resposta</span><span class="sxs-lookup"><span data-stu-id="444a8-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="444a8-326">IIS</span><span class="sxs-lookup"><span data-stu-id="444a8-326">IIS</span></span>

<span data-ttu-id="444a8-327">O limite de solicitação padrão ( `maxAllowedContentLength` ) é 30 milhões bytes, que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="444a8-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="444a8-328">Personalize o limite no `web.config` arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-328">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="444a8-329">No exemplo a seguir, o limite é definido como 50 MB (52.428.800 bytes):</span><span class="sxs-lookup"><span data-stu-id="444a8-329">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="444a8-330">A `maxAllowedContentLength` configuração se aplica somente ao IIS.</span><span class="sxs-lookup"><span data-stu-id="444a8-330">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="444a8-331">Para obter mais informações, consulte [limites `<requestLimits>` de solicitação ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="444a8-331">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="444a8-332">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="444a8-332">Troubleshoot</span></span>

<span data-ttu-id="444a8-333">Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.</span><span class="sxs-lookup"><span data-stu-id="444a8-333">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="444a8-334">Erro não encontrado quando implantado em um servidor IIS</span><span class="sxs-lookup"><span data-stu-id="444a8-334">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="444a8-335">O erro a seguir indica que o arquivo carregado excede o tamanho do conteúdo configurado do servidor:</span><span class="sxs-lookup"><span data-stu-id="444a8-335">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="444a8-336">Para obter mais informações, consulte a seção [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="444a8-336">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="444a8-337">Falha de conexão</span><span class="sxs-lookup"><span data-stu-id="444a8-337">Connection failure</span></span>

<span data-ttu-id="444a8-338">Um erro de conexão e uma conexão de servidor de redefinição provavelmente indicam que o arquivo carregado excede o tamanho máximo do corpo de solicitação do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="444a8-338">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="444a8-339">Para obter mais informações, consulte a seção [tamanho máximo de corpo de solicitação do Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="444a8-339">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="444a8-340">Os limites de conexão de cliente Kestrel também podem exigir ajuste.</span><span class="sxs-lookup"><span data-stu-id="444a8-340">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="444a8-341">Exceção de referência nula com IFormFile</span><span class="sxs-lookup"><span data-stu-id="444a8-341">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="444a8-342">Se o controlador estiver aceitando arquivos carregados usando <xref:Microsoft.AspNetCore.Http.IFormFile> , mas o valor for `null` , confirme se o formulário HTML está especificando um `enctype` valor de `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="444a8-342">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="444a8-343">Se esse atributo não estiver definido no `<form>` elemento, o upload do arquivo não ocorrerá e todos os <xref:Microsoft.AspNetCore.Http.IFormFile> argumentos associados serão `null` .</span><span class="sxs-lookup"><span data-stu-id="444a8-343">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="444a8-344">Além disso, confirme se a [nomenclatura de carregamento em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="444a8-344">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="444a8-345">O fluxo era muito longo</span><span class="sxs-lookup"><span data-stu-id="444a8-345">Stream was too long</span></span>

<span data-ttu-id="444a8-346">Os exemplos neste tópico dependem de <xref:System.IO.MemoryStream> manter o conteúdo do arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="444a8-346">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="444a8-347">O limite de tamanho de um `MemoryStream` é `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="444a8-347">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="444a8-348">Se o cenário de upload de arquivo do aplicativo exigir a retenção de conteúdo de arquivo com mais de 50 MB, use uma abordagem alternativa que não dependa de um único `MemoryStream` para manter o conteúdo de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="444a8-348">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="444a8-349">O ASP.NET Core dá suporte ao carregamento de um ou mais arquivos usando a associação de modelo em buffer para arquivos menores e streaming sem buffer para arquivos maiores.</span><span class="sxs-lookup"><span data-stu-id="444a8-349">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="444a8-350">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="444a8-350">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="444a8-351">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="444a8-351">Security considerations</span></span>

<span data-ttu-id="444a8-352">Tome cuidado ao fornecer aos usuários a capacidade de carregar arquivos em um servidor.</span><span class="sxs-lookup"><span data-stu-id="444a8-352">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="444a8-353">Os invasores podem tentar:</span><span class="sxs-lookup"><span data-stu-id="444a8-353">Attackers may attempt to:</span></span>

* <span data-ttu-id="444a8-354">Executar ataques [de negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="444a8-354">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="444a8-355">Carregar vírus ou malware.</span><span class="sxs-lookup"><span data-stu-id="444a8-355">Upload viruses or malware.</span></span>
* <span data-ttu-id="444a8-356">Comprometa redes e servidores de outras maneiras.</span><span class="sxs-lookup"><span data-stu-id="444a8-356">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="444a8-357">As etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:</span><span class="sxs-lookup"><span data-stu-id="444a8-357">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="444a8-358">Carregue arquivos em uma área de carregamento de arquivo dedicada, preferencialmente para uma unidade que não seja do sistema.</span><span class="sxs-lookup"><span data-stu-id="444a8-358">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="444a8-359">Um local dedicado torna mais fácil impor restrições de segurança em arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-359">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="444a8-360">Desabilite as permissões de execução no local de carregamento do arquivo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="444a8-360">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="444a8-361">**Não** persiste arquivos carregados na mesma árvore de diretório que o aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="444a8-361">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="444a8-362">Use um nome de arquivo seguro determinado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-362">Use a safe file name determined by the app.</span></span> <span data-ttu-id="444a8-363">Não use um nome de arquivo fornecido pelo usuário ou o nome de arquivo não confiável do arquivo carregado. &dagger; O HTML codifica o nome de arquivo não confiável ao exibi-lo.</span><span class="sxs-lookup"><span data-stu-id="444a8-363">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="444a8-364">Por exemplo, registrar em log o nome do arquivo ou exibindo na interface do usuário (a Razor saída de código HTML automaticamente).</span><span class="sxs-lookup"><span data-stu-id="444a8-364">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="444a8-365">Permitir somente extensões de arquivo aprovadas para a especificação de design do aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="444a8-365">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="444a8-366">Verifique se as verificações do lado do cliente são executadas no servidor. &dagger; As verificações no lado do cliente são fáceis de evitar.</span><span class="sxs-lookup"><span data-stu-id="444a8-366">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="444a8-367">Verifique o tamanho de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="444a8-367">Check the size of an uploaded file.</span></span> <span data-ttu-id="444a8-368">Defina um limite de tamanho máximo para evitar carregamentos grandes.&dagger;</span><span class="sxs-lookup"><span data-stu-id="444a8-368">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="444a8-369">Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-369">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="444a8-370">**Execute um scanner de vírus/malware no conteúdo carregado antes de o arquivo ser armazenado.**</span><span class="sxs-lookup"><span data-stu-id="444a8-370">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="444a8-371">&dagger;O aplicativo de exemplo demonstra uma abordagem que atende aos critérios.</span><span class="sxs-lookup"><span data-stu-id="444a8-371">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="444a8-372">Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:</span><span class="sxs-lookup"><span data-stu-id="444a8-372">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="444a8-373">Assuma completamente o controle de um sistema.</span><span class="sxs-lookup"><span data-stu-id="444a8-373">Completely gain control of a system.</span></span>
> * <span data-ttu-id="444a8-374">Sobrecarregar um sistema com o resultado que o sistema falha.</span><span class="sxs-lookup"><span data-stu-id="444a8-374">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="444a8-375">Comprometer dados do sistema ou de usuários.</span><span class="sxs-lookup"><span data-stu-id="444a8-375">Compromise user or system data.</span></span>
> * <span data-ttu-id="444a8-376">Aplique graffiti a uma interface do usuário pública.</span><span class="sxs-lookup"><span data-stu-id="444a8-376">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="444a8-377">Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="444a8-377">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="444a8-378">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="444a8-378">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="444a8-379">Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários</span><span class="sxs-lookup"><span data-stu-id="444a8-379">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="444a8-380">Para obter mais informações sobre como implementar medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [validação](#validation) .</span><span class="sxs-lookup"><span data-stu-id="444a8-380">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="444a8-381">Cenários de armazenamento</span><span class="sxs-lookup"><span data-stu-id="444a8-381">Storage scenarios</span></span>

<span data-ttu-id="444a8-382">As opções de armazenamento comuns para arquivos incluem:</span><span class="sxs-lookup"><span data-stu-id="444a8-382">Common storage options for files include:</span></span>

* <span data-ttu-id="444a8-383">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="444a8-383">Database</span></span>

  * <span data-ttu-id="444a8-384">Para carregamentos de arquivos pequenos, um banco de dados geralmente é mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).</span><span class="sxs-lookup"><span data-stu-id="444a8-384">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="444a8-385">Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, pois a recuperação de um registro de banco de dados para o usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de Avatar).</span><span class="sxs-lookup"><span data-stu-id="444a8-385">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="444a8-386">Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dado.</span><span class="sxs-lookup"><span data-stu-id="444a8-386">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="444a8-387">Armazenamento físico (sistema de arquivos ou compartilhamento de rede)</span><span class="sxs-lookup"><span data-stu-id="444a8-387">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="444a8-388">Para carregamentos de arquivos grandes:</span><span class="sxs-lookup"><span data-stu-id="444a8-388">For large file uploads:</span></span>
    * <span data-ttu-id="444a8-389">Os limites de banco de dados podem restringir o tamanho do carregamento.</span><span class="sxs-lookup"><span data-stu-id="444a8-389">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="444a8-390">O armazenamento físico é geralmente menos econômico do que o armazenamento em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="444a8-390">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="444a8-391">O armazenamento físico é potencialmente mais barato do que usar um serviço de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="444a8-391">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="444a8-392">O processo do aplicativo deve ter permissões de leitura e gravação no local de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="444a8-392">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="444a8-393">**Nunca conceda a permissão EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="444a8-393">**Never grant execute permission.**</span></span>

* <span data-ttu-id="444a8-394">Serviço de armazenamento de dados (por exemplo, [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="444a8-394">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="444a8-395">Os serviços geralmente oferecem escalabilidade e resiliência aprimoradas sobre soluções locais que geralmente estão sujeitas a pontos únicos de falha.</span><span class="sxs-lookup"><span data-stu-id="444a8-395">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="444a8-396">Os serviços são um custo potencialmente menor em cenários de infraestrutura de armazenamento grandes.</span><span class="sxs-lookup"><span data-stu-id="444a8-396">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="444a8-397">Para obter mais informações, consulte [início rápido: usar o .net para criar um blob no armazenamento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="444a8-397">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="444a8-398">Cenários de upload de arquivo</span><span class="sxs-lookup"><span data-stu-id="444a8-398">File upload scenarios</span></span>

<span data-ttu-id="444a8-399">Duas abordagens gerais para carregar arquivos são buffer e streaming.</span><span class="sxs-lookup"><span data-stu-id="444a8-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="444a8-400">**de resposta**</span><span class="sxs-lookup"><span data-stu-id="444a8-400">**Buffering**</span></span>

<span data-ttu-id="444a8-401">O arquivo inteiro é lido em um <xref:Microsoft.AspNetCore.Http.IFormFile> , que é uma representação em C# do arquivo usado para processar ou salvar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="444a8-402">Os recursos (disco, memória) usados por carregamentos de arquivos dependem do número e do tamanho de carregamentos de arquivos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="444a8-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="444a8-403">Se um aplicativo tentar armazenar em buffer um número excessivo de carregamentos, o site falhará quando ficar sem memória ou espaço em disco.</span><span class="sxs-lookup"><span data-stu-id="444a8-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="444a8-404">Se o tamanho ou a frequência dos carregamentos de arquivos estiver esgotando os recursos do aplicativo, use o streaming.</span><span class="sxs-lookup"><span data-stu-id="444a8-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="444a8-405">Qualquer arquivo em buffer único que exceda 64 KB é movido da memória para um arquivo temporário no disco.</span><span class="sxs-lookup"><span data-stu-id="444a8-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="444a8-406">O armazenamento em buffer de arquivos pequenos é abordado nas seguintes seções deste tópico:</span><span class="sxs-lookup"><span data-stu-id="444a8-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="444a8-407">Armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="444a8-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="444a8-408">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="444a8-408">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="444a8-409">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="444a8-409">**Streaming**</span></span>

<span data-ttu-id="444a8-410">O arquivo é recebido de uma solicitação de várias partes e processada ou salva diretamente pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="444a8-411">O streaming não melhora significativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="444a8-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="444a8-412">O streaming reduz as demandas de memória ou espaço em disco ao carregar arquivos.</span><span class="sxs-lookup"><span data-stu-id="444a8-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="444a8-413">O streaming de arquivos grandes é abordado na seção [carregar arquivos grandes com streaming](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="444a8-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="444a8-414">Carregar arquivos pequenos com associação de modelo em buffer para armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="444a8-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="444a8-415">Para carregar arquivos pequenos, use um formulário com diversas partes ou Construa uma solicitação POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="444a8-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="444a8-416">O exemplo a seguir demonstra o uso de um Razor formulário de páginas para carregar um único arquivo (*pages/BufferedSingleFileUploadPhysical. cshtml* no aplicativo de exemplo):</span><span class="sxs-lookup"><span data-stu-id="444a8-416">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="444a8-417">O exemplo a seguir é análogo ao exemplo anterior, exceto pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="444a8-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="444a8-418">O JavaScript ([buscar API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) é usado para enviar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="444a8-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="444a8-419">Não há validação.</span><span class="sxs-lookup"><span data-stu-id="444a8-419">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="444a8-420">Para executar a POSTAgem de formulário no JavaScript para clientes que [não dão suporte à API de busca](https://caniuse.com/#feat=fetch), use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="444a8-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="444a8-421">Use um polifill de busca (por exemplo, [janela. FETCH retroativo (github/FETCH)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="444a8-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="444a8-422">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="444a8-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="444a8-423">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="444a8-423">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="444a8-424">Para dar suporte a uploads de arquivo, os formulários HTML devem especificar um tipo de codificação ( `enctype` ) de `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="444a8-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="444a8-425">Para um `files` elemento input oferecer suporte ao carregamento de vários arquivos, forneça o `multiple` atributo no `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="444a8-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="444a8-426">Os arquivos individuais carregados no servidor podem ser acessados por meio de [Associação de modelo](xref:mvc/models/model-binding) usando <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="444a8-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="444a8-427">O aplicativo de exemplo demonstra vários uploads de arquivo em buffer para cenários de armazenamento físico e banco de dados.</span><span class="sxs-lookup"><span data-stu-id="444a8-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="444a8-428">**Não** use a `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> outros para exibição e registro em log.</span><span class="sxs-lookup"><span data-stu-id="444a8-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="444a8-429">Ao exibir ou registrar em log, o HTML codifica o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="444a8-430">Um invasor pode fornecer um nome de arquivo mal-intencionado, incluindo caminhos completos ou caminhos relativos.</span><span class="sxs-lookup"><span data-stu-id="444a8-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="444a8-431">Os aplicativos devem:</span><span class="sxs-lookup"><span data-stu-id="444a8-431">Applications should:</span></span>
>
> * <span data-ttu-id="444a8-432">Remova o caminho do nome de arquivo fornecido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="444a8-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="444a8-433">Salve o nome de arquivo com codificação em HTML e removido por caminho para a interface do usuário ou log.</span><span class="sxs-lookup"><span data-stu-id="444a8-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="444a8-434">Gere um novo nome de arquivo aleatório para armazenamento.</span><span class="sxs-lookup"><span data-stu-id="444a8-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="444a8-435">O código a seguir remove o caminho do nome do arquivo:</span><span class="sxs-lookup"><span data-stu-id="444a8-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="444a8-436">Os exemplos fornecidos até o momento não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="444a8-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="444a8-437">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-437">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="444a8-438">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="444a8-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="444a8-439">Validação</span><span class="sxs-lookup"><span data-stu-id="444a8-439">Validation</span></span>](#validation)

<span data-ttu-id="444a8-440">Ao carregar arquivos usando a associação de modelo e <xref:Microsoft.AspNetCore.Http.IFormFile> o método de ação pode aceitar:</span><span class="sxs-lookup"><span data-stu-id="444a8-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="444a8-441">Um único <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="444a8-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="444a8-442">Qualquer uma das seguintes coleções que representam vários arquivos:</span><span class="sxs-lookup"><span data-stu-id="444a8-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="444a8-443">[Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="444a8-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="444a8-444">A Associação corresponde aos arquivos de formulário por nome.</span><span class="sxs-lookup"><span data-stu-id="444a8-444">Binding matches form files by name.</span></span> <span data-ttu-id="444a8-445">Por exemplo, o `name` valor HTML in `<input type="file" name="formFile">` deve corresponder ao parâmetro/Propriedade do C# associado ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="444a8-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="444a8-446">Para obter mais informações, consulte o [valor do atributo Match Name para o nome do parâmetro da seção do método post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="444a8-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="444a8-447">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-447">The following example:</span></span>

* <span data-ttu-id="444a8-448">Executa um loop em um ou mais arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="444a8-449">Usa [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="444a8-450">Salva os arquivos no sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="444a8-451">Retorna o número total e o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-451">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="444a8-452">Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho.</span><span class="sxs-lookup"><span data-stu-id="444a8-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="444a8-453">No exemplo a seguir, o caminho é obtido da configuração:</span><span class="sxs-lookup"><span data-stu-id="444a8-453">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="444a8-454">O caminho passado para o <xref:System.IO.FileStream> *deve* incluir o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="444a8-455">Se o nome do arquivo não for fornecido, um <xref:System.UnauthorizedAccessException> será lançado no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="444a8-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="444a8-456">Os arquivos carregados usando a <xref:Microsoft.AspNetCore.Http.IFormFile> técnica são armazenados em buffer na memória ou no disco no servidor antes do processamento.</span><span class="sxs-lookup"><span data-stu-id="444a8-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="444a8-457">Dentro do método de ação, o <xref:Microsoft.AspNetCore.Http.IFormFile> conteúdo é acessível como um <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="444a8-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="444a8-458">Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como o [armazenamento de BLOBs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="444a8-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="444a8-459">Para outro exemplo que executa um loop sobre vários arquivos para carregar e usa nomes de arquivo seguros, consulte *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="444a8-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="444a8-460">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) gera um <xref:System.IO.IOException> se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores.</span><span class="sxs-lookup"><span data-stu-id="444a8-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="444a8-461">O limite de 65.535 arquivos é um limite por servidor.</span><span class="sxs-lookup"><span data-stu-id="444a8-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="444a8-462">Para obter mais informações sobre esse limite no sistema operacional Windows, consulte os comentários nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="444a8-463">Função GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="444a8-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="444a8-464">Carregar arquivos pequenos com associação de modelo em buffer para um banco de dados</span><span class="sxs-lookup"><span data-stu-id="444a8-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="444a8-465">Para armazenar dados de arquivo binário em um banco de dado usando [Entity Framework](/ef/core/index), defina uma <xref:System.Byte> propriedade de matriz na entidade:</span><span class="sxs-lookup"><span data-stu-id="444a8-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="444a8-466">Especifique uma propriedade de modelo de página para a classe que inclui um <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="444a8-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="444a8-467"><xref:Microsoft.AspNetCore.Http.IFormFile> pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="444a8-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="444a8-468">O exemplo anterior usa uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="444a8-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="444a8-469">O `FileUpload` é usado no Razor formulário páginas:</span><span class="sxs-lookup"><span data-stu-id="444a8-469">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="444a8-470">Quando o formulário for Postado no servidor, copie o <xref:Microsoft.AspNetCore.Http.IFormFile> para um fluxo e salve-o como uma matriz de bytes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="444a8-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="444a8-471">No exemplo a seguir, `_dbContext` armazena o contexto do banco de dados do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="444a8-471">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="444a8-472">O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="444a8-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="444a8-473">*Páginas/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="444a8-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="444a8-474">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="444a8-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="444a8-475">Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="444a8-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="444a8-476">Não confie ou confie na `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação.</span><span class="sxs-lookup"><span data-stu-id="444a8-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="444a8-477">A `FileName` propriedade só deve ser usada para fins de exibição e somente após a codificação HTML.</span><span class="sxs-lookup"><span data-stu-id="444a8-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="444a8-478">Os exemplos fornecidos não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="444a8-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="444a8-479">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-479">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="444a8-480">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="444a8-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="444a8-481">Validação</span><span class="sxs-lookup"><span data-stu-id="444a8-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="444a8-482">Carregar arquivos grandes com streaming</span><span class="sxs-lookup"><span data-stu-id="444a8-482">Upload large files with streaming</span></span>

<span data-ttu-id="444a8-483">O exemplo a seguir demonstra como usar o JavaScript para transmitir um arquivo a uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="444a8-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="444a8-484">O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="444a8-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="444a8-485">Como o método de ação processa diretamente os dados carregados, a associação de modelo de formulário é desabilitada por outro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="444a8-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="444a8-486">Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="444a8-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="444a8-487">Depois que as seções com várias partes forem lidas, a ação executará sua própria associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="444a8-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="444a8-488">A resposta de página inicial carrega o formulário e salva um token de antifalsificação em um cookie (por meio do `GenerateAntiforgeryTokenCookieAttribute` atributo).</span><span class="sxs-lookup"><span data-stu-id="444a8-488">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="444a8-489">O atributo usa o [suporte de antifalsificação](xref:security/anti-request-forgery) interno do ASP.NET Core para definir um cookie com um token de solicitação:</span><span class="sxs-lookup"><span data-stu-id="444a8-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="444a8-490">O `DisableFormValueModelBindingAttribute` é usado para desabilitar a associação de modelo:</span><span class="sxs-lookup"><span data-stu-id="444a8-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="444a8-491">No aplicativo de exemplo, `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` são aplicados como filtros aos modelos de aplicativo de página do `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` no `Startup.ConfigureServices` usando convenções de [ Razor páginas](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="444a8-491">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="444a8-492">Como a associação de modelo não lê o formulário, os parâmetros que estão associados do formulário não são associados (a consulta, a rota e o cabeçalho continuam a funcionar).</span><span class="sxs-lookup"><span data-stu-id="444a8-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="444a8-493">O método de ação funciona diretamente com a `Request` propriedade.</span><span class="sxs-lookup"><span data-stu-id="444a8-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="444a8-494">Um `MultipartReader` é usado para ler cada seção.</span><span class="sxs-lookup"><span data-stu-id="444a8-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="444a8-495">Os dados de chave/valor são armazenados em um `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="444a8-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="444a8-496">Depois que as seções com várias partes forem lidas, o conteúdo do `KeyValueAccumulator` será usado para associar os dados do formulário a um tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="444a8-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="444a8-497">O `StreamingController.UploadDatabase` método completo para streaming para um banco de dados com EF Core:</span><span class="sxs-lookup"><span data-stu-id="444a8-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="444a8-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="444a8-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="444a8-499">O `StreamingController.UploadPhysical` método completo para streaming para um local físico:</span><span class="sxs-lookup"><span data-stu-id="444a8-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="444a8-500">No aplicativo de exemplo, as verificações de validação são tratadas pelo `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="444a8-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="444a8-501">Validação</span><span class="sxs-lookup"><span data-stu-id="444a8-501">Validation</span></span>

<span data-ttu-id="444a8-502">A classe do aplicativo de exemplo `FileHelpers` demonstra várias verificações para <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivos em buffer e em fluxo.</span><span class="sxs-lookup"><span data-stu-id="444a8-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="444a8-503">Para processar <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivo em buffer no aplicativo de exemplo, consulte o `ProcessFormFile` método no arquivo *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="444a8-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="444a8-504">Para o processamento de arquivos transmitidos, consulte o `ProcessStreamedFile` método no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="444a8-505">Os métodos de processamento de validação demonstrados no aplicativo de exemplo não verificam o conteúdo dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="444a8-506">Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para os usuários ou outros sistemas.</span><span class="sxs-lookup"><span data-stu-id="444a8-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="444a8-507">Embora o tópico exemplo forneça um exemplo de trabalho de técnicas de validação, não implemente a `FileHelpers` classe em um aplicativo de produção, a menos que você:</span><span class="sxs-lookup"><span data-stu-id="444a8-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="444a8-508">Entenda totalmente a implementação.</span><span class="sxs-lookup"><span data-stu-id="444a8-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="444a8-509">Modifique a implementação conforme apropriado para o ambiente e as especificações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="444a8-510">**Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**</span><span class="sxs-lookup"><span data-stu-id="444a8-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="444a8-511">Validação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="444a8-511">Content validation</span></span>

<span data-ttu-id="444a8-512">**Use uma API de verificação de vírus/malware de terceiros no conteúdo carregado.**</span><span class="sxs-lookup"><span data-stu-id="444a8-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="444a8-513">A verificação de arquivos exige muitos recursos do servidor em cenários de alto volume.</span><span class="sxs-lookup"><span data-stu-id="444a8-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="444a8-514">Se o desempenho do processamento de solicitações for reduzido devido à verificação de arquivo, considere descarregar o trabalho de verificação para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="444a8-515">Normalmente, os arquivos carregados são mantidos em uma área em quarentena até que o verificador de vírus em segundo plano os Verifique.</span><span class="sxs-lookup"><span data-stu-id="444a8-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="444a8-516">Quando um arquivo passa, o arquivo é movido para o local de armazenamento de arquivo normal.</span><span class="sxs-lookup"><span data-stu-id="444a8-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="444a8-517">Essas etapas geralmente são executadas em conjunto com um registro de banco de dados que indica o status de verificação de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="444a8-518">Ao usar essa abordagem, o aplicativo e o servidor de aplicativos permanecem concentrados em responder às solicitações.</span><span class="sxs-lookup"><span data-stu-id="444a8-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="444a8-519">Validação de extensão de arquivo</span><span class="sxs-lookup"><span data-stu-id="444a8-519">File extension validation</span></span>

<span data-ttu-id="444a8-520">A extensão do arquivo carregado deve ser verificada em uma lista de extensões permitidas.</span><span class="sxs-lookup"><span data-stu-id="444a8-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="444a8-521">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="444a8-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="444a8-522">Validação de assinatura de arquivo</span><span class="sxs-lookup"><span data-stu-id="444a8-522">File signature validation</span></span>

<span data-ttu-id="444a8-523">A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="444a8-524">Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="444a8-525">O aplicativo de exemplo verifica as assinaturas de arquivo para alguns tipos de arquivo comuns.</span><span class="sxs-lookup"><span data-stu-id="444a8-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="444a8-526">No exemplo a seguir, a assinatura de arquivo para uma imagem JPEG é verificada em relação ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="444a8-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="444a8-527">Para obter assinaturas de arquivo adicionais, consulte o [banco de dados assinaturas de arquivo](https://www.filesignatures.net/) e especificações de arquivo oficial.</span><span class="sxs-lookup"><span data-stu-id="444a8-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="444a8-528">Segurança de nome de arquivo</span><span class="sxs-lookup"><span data-stu-id="444a8-528">File name security</span></span>

<span data-ttu-id="444a8-529">Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo no armazenamento físico.</span><span class="sxs-lookup"><span data-stu-id="444a8-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="444a8-530">Crie um nome de arquivo seguro para o arquivo usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.</span><span class="sxs-lookup"><span data-stu-id="444a8-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="444a8-531">Razor o HTML automaticamente codifica valores de propriedade para exibição.</span><span class="sxs-lookup"><span data-stu-id="444a8-531">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="444a8-532">O código a seguir é seguro para usar:</span><span class="sxs-lookup"><span data-stu-id="444a8-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="444a8-533">Fora do Razor , sempre <xref:System.Net.WebUtility.HtmlEncode*> nome do arquivo o conteúdo da solicitação de um usuário.</span><span class="sxs-lookup"><span data-stu-id="444a8-533">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="444a8-534">Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo será substituído por um arquivo com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="444a8-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="444a8-535">Forneça lógica adicional para atender às especificações do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="444a8-536">Validação de tamanho</span><span class="sxs-lookup"><span data-stu-id="444a8-536">Size validation</span></span>

<span data-ttu-id="444a8-537">Limite o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="444a8-538">No aplicativo de exemplo, o tamanho do arquivo é limitado a 2 MB (indicado em bytes).</span><span class="sxs-lookup"><span data-stu-id="444a8-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="444a8-539">O limite é fornecido por meio da [configuração](xref:fundamentals/configuration/index) do *appsettings.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="444a8-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="444a8-540">O `FileSizeLimit` é injetado em `PageModel` classes:</span><span class="sxs-lookup"><span data-stu-id="444a8-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="444a8-541">Quando um tamanho de arquivo exceder o limite, o arquivo será rejeitado:</span><span class="sxs-lookup"><span data-stu-id="444a8-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="444a8-542">Coincidir valor do atributo de nome com o nome do parâmetro do método POST</span><span class="sxs-lookup"><span data-stu-id="444a8-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="444a8-543">Em não Razor formulários que postam dados de formulário ou usam o JavaScript `FormData` diretamente, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="444a8-543">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="444a8-544">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-544">In the following example:</span></span>

* <span data-ttu-id="444a8-545">Ao usar um `<input>` elemento, o `name` atributo é definido como o valor `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="444a8-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="444a8-546">Ao usar `FormData` em JavaScript, o nome é definido como o valor `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="444a8-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="444a8-547">Use um nome correspondente para o parâmetro do método C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="444a8-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="444a8-548">Para um Razor método de manipulador de página de páginas chamado `Upload` :</span><span class="sxs-lookup"><span data-stu-id="444a8-548">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="444a8-549">Para um método de ação do controlador de POSTAgem MVC:</span><span class="sxs-lookup"><span data-stu-id="444a8-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="444a8-550">Configuração de servidor e aplicativo</span><span class="sxs-lookup"><span data-stu-id="444a8-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="444a8-551">Limite de comprimento de corpo com várias partes</span><span class="sxs-lookup"><span data-stu-id="444a8-551">Multipart body length limit</span></span>

<span data-ttu-id="444a8-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> define o limite para o comprimento de cada corpo com diversas partes.</span><span class="sxs-lookup"><span data-stu-id="444a8-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="444a8-553">As seções de formulário que excedem esse limite lançam um <xref:System.IO.InvalidDataException> quando analisado.</span><span class="sxs-lookup"><span data-stu-id="444a8-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="444a8-554">O padrão é 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="444a8-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="444a8-555">Personalize o limite usando a <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> configuração em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="444a8-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="444a8-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> é usado para definir o <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="444a8-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="444a8-557">Em um Razor aplicativo de páginas, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="444a8-557">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

<span data-ttu-id="444a8-558">Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="444a8-558">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="444a8-559">Tamanho máximo do corpo de solicitação do Kestrel</span><span class="sxs-lookup"><span data-stu-id="444a8-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="444a8-560">Para aplicativos hospedados pelo Kestrel, o tamanho máximo padrão do corpo da solicitação é 30 milhões bytes, que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="444a8-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="444a8-561">Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="444a8-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="444a8-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="444a8-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="444a8-563">Em um Razor aplicativo de páginas, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="444a8-563">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

<span data-ttu-id="444a8-564">Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro à classe de manipulador de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="444a8-564">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="444a8-565">O `RequestSizeLimitAttribute` também pode ser aplicado usando a [`@attribute`](xref:mvc/views/razor#attribute) Razor diretiva:</span><span class="sxs-lookup"><span data-stu-id="444a8-565">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="444a8-566">Outros limites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="444a8-566">Other Kestrel limits</span></span>

<span data-ttu-id="444a8-567">Outros limites de Kestrel podem ser aplicados para aplicativos hospedados pelo Kestrel:</span><span class="sxs-lookup"><span data-stu-id="444a8-567">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="444a8-568">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="444a8-568">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="444a8-569">Taxas de dados de solicitação e resposta</span><span class="sxs-lookup"><span data-stu-id="444a8-569">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="444a8-570">IIS</span><span class="sxs-lookup"><span data-stu-id="444a8-570">IIS</span></span>

<span data-ttu-id="444a8-571">O limite de solicitação padrão ( `maxAllowedContentLength` ) é 30 milhões bytes, que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="444a8-571">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="444a8-572">Personalize o limite no `web.config` arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-572">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="444a8-573">No exemplo a seguir, o limite é definido como 50 MB (52.428.800 bytes):</span><span class="sxs-lookup"><span data-stu-id="444a8-573">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="444a8-574">A `maxAllowedContentLength` configuração se aplica somente ao IIS.</span><span class="sxs-lookup"><span data-stu-id="444a8-574">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="444a8-575">Para obter mais informações, consulte [limites `<requestLimits>` de solicitação ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="444a8-575">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="444a8-576">Aumente o tamanho máximo do corpo de solicitação para a solicitação HTTP definindo <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="444a8-576">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="444a8-577">No exemplo a seguir, o limite é definido como 50 MB (52.428.800 bytes):</span><span class="sxs-lookup"><span data-stu-id="444a8-577">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="444a8-578">Para obter mais informações, consulte <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="444a8-578">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="444a8-579">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="444a8-579">Troubleshoot</span></span>

<span data-ttu-id="444a8-580">Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.</span><span class="sxs-lookup"><span data-stu-id="444a8-580">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="444a8-581">Erro não encontrado quando implantado em um servidor IIS</span><span class="sxs-lookup"><span data-stu-id="444a8-581">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="444a8-582">O erro a seguir indica que o arquivo carregado excede o tamanho do conteúdo configurado do servidor:</span><span class="sxs-lookup"><span data-stu-id="444a8-582">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="444a8-583">Para obter mais informações, consulte a seção [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="444a8-583">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="444a8-584">Falha de conexão</span><span class="sxs-lookup"><span data-stu-id="444a8-584">Connection failure</span></span>

<span data-ttu-id="444a8-585">Um erro de conexão e uma conexão de servidor de redefinição provavelmente indicam que o arquivo carregado excede o tamanho máximo do corpo de solicitação do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="444a8-585">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="444a8-586">Para obter mais informações, consulte a seção [tamanho máximo de corpo de solicitação do Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="444a8-586">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="444a8-587">Os limites de conexão de cliente Kestrel também podem exigir ajuste.</span><span class="sxs-lookup"><span data-stu-id="444a8-587">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="444a8-588">Exceção de referência nula com IFormFile</span><span class="sxs-lookup"><span data-stu-id="444a8-588">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="444a8-589">Se o controlador estiver aceitando arquivos carregados usando <xref:Microsoft.AspNetCore.Http.IFormFile> , mas o valor for `null` , confirme se o formulário HTML está especificando um `enctype` valor de `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="444a8-589">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="444a8-590">Se esse atributo não estiver definido no `<form>` elemento, o upload do arquivo não ocorrerá e todos os <xref:Microsoft.AspNetCore.Http.IFormFile> argumentos associados serão `null` .</span><span class="sxs-lookup"><span data-stu-id="444a8-590">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="444a8-591">Além disso, confirme se a [nomenclatura de carregamento em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="444a8-591">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="444a8-592">O fluxo era muito longo</span><span class="sxs-lookup"><span data-stu-id="444a8-592">Stream was too long</span></span>

<span data-ttu-id="444a8-593">Os exemplos neste tópico dependem de <xref:System.IO.MemoryStream> manter o conteúdo do arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="444a8-593">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="444a8-594">O limite de tamanho de um `MemoryStream` é `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="444a8-594">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="444a8-595">Se o cenário de upload de arquivo do aplicativo exigir a retenção de conteúdo de arquivo com mais de 50 MB, use uma abordagem alternativa que não dependa de um único `MemoryStream` para manter o conteúdo de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="444a8-595">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="444a8-596">O ASP.NET Core dá suporte ao carregamento de um ou mais arquivos usando a associação de modelo em buffer para arquivos menores e streaming sem buffer para arquivos maiores.</span><span class="sxs-lookup"><span data-stu-id="444a8-596">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="444a8-597">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="444a8-597">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="444a8-598">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="444a8-598">Security considerations</span></span>

<span data-ttu-id="444a8-599">Tome cuidado ao fornecer aos usuários a capacidade de carregar arquivos em um servidor.</span><span class="sxs-lookup"><span data-stu-id="444a8-599">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="444a8-600">Os invasores podem tentar:</span><span class="sxs-lookup"><span data-stu-id="444a8-600">Attackers may attempt to:</span></span>

* <span data-ttu-id="444a8-601">Executar ataques [de negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) .</span><span class="sxs-lookup"><span data-stu-id="444a8-601">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="444a8-602">Carregar vírus ou malware.</span><span class="sxs-lookup"><span data-stu-id="444a8-602">Upload viruses or malware.</span></span>
* <span data-ttu-id="444a8-603">Comprometa redes e servidores de outras maneiras.</span><span class="sxs-lookup"><span data-stu-id="444a8-603">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="444a8-604">As etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:</span><span class="sxs-lookup"><span data-stu-id="444a8-604">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="444a8-605">Carregue arquivos em uma área de carregamento de arquivo dedicada, preferencialmente para uma unidade que não seja do sistema.</span><span class="sxs-lookup"><span data-stu-id="444a8-605">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="444a8-606">Um local dedicado torna mais fácil impor restrições de segurança em arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-606">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="444a8-607">Desabilite as permissões de execução no local de carregamento do arquivo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="444a8-607">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="444a8-608">**Não** persiste arquivos carregados na mesma árvore de diretório que o aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="444a8-608">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="444a8-609">Use um nome de arquivo seguro determinado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-609">Use a safe file name determined by the app.</span></span> <span data-ttu-id="444a8-610">Não use um nome de arquivo fornecido pelo usuário ou o nome de arquivo não confiável do arquivo carregado. &dagger; O HTML codifica o nome de arquivo não confiável ao exibi-lo.</span><span class="sxs-lookup"><span data-stu-id="444a8-610">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="444a8-611">Por exemplo, registrar em log o nome do arquivo ou exibindo na interface do usuário (a Razor saída de código HTML automaticamente).</span><span class="sxs-lookup"><span data-stu-id="444a8-611">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="444a8-612">Permitir somente extensões de arquivo aprovadas para a especificação de design do aplicativo.&dagger;</span><span class="sxs-lookup"><span data-stu-id="444a8-612">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="444a8-613">Verifique se as verificações do lado do cliente são executadas no servidor. &dagger; As verificações no lado do cliente são fáceis de evitar.</span><span class="sxs-lookup"><span data-stu-id="444a8-613">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="444a8-614">Verifique o tamanho de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="444a8-614">Check the size of an uploaded file.</span></span> <span data-ttu-id="444a8-615">Defina um limite de tamanho máximo para evitar carregamentos grandes.&dagger;</span><span class="sxs-lookup"><span data-stu-id="444a8-615">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="444a8-616">Quando os arquivos não devem ser substituídos por um arquivo carregado com o mesmo nome, verifique o nome do arquivo no banco de dados ou no armazenamento físico antes de carregar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-616">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="444a8-617">**Execute um scanner de vírus/malware no conteúdo carregado antes de o arquivo ser armazenado.**</span><span class="sxs-lookup"><span data-stu-id="444a8-617">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="444a8-618">&dagger;O aplicativo de exemplo demonstra uma abordagem que atende aos critérios.</span><span class="sxs-lookup"><span data-stu-id="444a8-618">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="444a8-619">Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:</span><span class="sxs-lookup"><span data-stu-id="444a8-619">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="444a8-620">Assuma completamente o controle de um sistema.</span><span class="sxs-lookup"><span data-stu-id="444a8-620">Completely gain control of a system.</span></span>
> * <span data-ttu-id="444a8-621">Sobrecarregar um sistema com o resultado que o sistema falha.</span><span class="sxs-lookup"><span data-stu-id="444a8-621">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="444a8-622">Comprometer dados do sistema ou de usuários.</span><span class="sxs-lookup"><span data-stu-id="444a8-622">Compromise user or system data.</span></span>
> * <span data-ttu-id="444a8-623">Aplique graffiti a uma interface do usuário pública.</span><span class="sxs-lookup"><span data-stu-id="444a8-623">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="444a8-624">Para obter informações de como reduzir a área da superfície de ataque ao aceitar arquivos de usuários, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="444a8-624">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * <span data-ttu-id="444a8-625">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="444a8-625">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
> * [<span data-ttu-id="444a8-626">Segurança do Azure: Verifique se os controles adequados estão em vigor ao aceitar arquivos de usuários</span><span class="sxs-lookup"><span data-stu-id="444a8-626">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="444a8-627">Para obter mais informações sobre como implementar medidas de segurança, incluindo exemplos do aplicativo de exemplo, consulte a seção [validação](#validation) .</span><span class="sxs-lookup"><span data-stu-id="444a8-627">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="444a8-628">Cenários de armazenamento</span><span class="sxs-lookup"><span data-stu-id="444a8-628">Storage scenarios</span></span>

<span data-ttu-id="444a8-629">As opções de armazenamento comuns para arquivos incluem:</span><span class="sxs-lookup"><span data-stu-id="444a8-629">Common storage options for files include:</span></span>

* <span data-ttu-id="444a8-630">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="444a8-630">Database</span></span>

  * <span data-ttu-id="444a8-631">Para carregamentos de arquivos pequenos, um banco de dados geralmente é mais rápido do que as opções de armazenamento físico (sistema de arquivos ou compartilhamento de rede).</span><span class="sxs-lookup"><span data-stu-id="444a8-631">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="444a8-632">Um banco de dados é muitas vezes mais conveniente do que as opções de armazenamento físico, pois a recuperação de um registro de banco de dados para o usuário pode fornecer simultaneamente o conteúdo do arquivo (por exemplo, uma imagem de Avatar).</span><span class="sxs-lookup"><span data-stu-id="444a8-632">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="444a8-633">Um banco de dados é potencialmente mais barato do que usar um serviço de armazenamento de dado.</span><span class="sxs-lookup"><span data-stu-id="444a8-633">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="444a8-634">Armazenamento físico (sistema de arquivos ou compartilhamento de rede)</span><span class="sxs-lookup"><span data-stu-id="444a8-634">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="444a8-635">Para carregamentos de arquivos grandes:</span><span class="sxs-lookup"><span data-stu-id="444a8-635">For large file uploads:</span></span>
    * <span data-ttu-id="444a8-636">Os limites de banco de dados podem restringir o tamanho do carregamento.</span><span class="sxs-lookup"><span data-stu-id="444a8-636">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="444a8-637">O armazenamento físico é geralmente menos econômico do que o armazenamento em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="444a8-637">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="444a8-638">O armazenamento físico é potencialmente mais barato do que usar um serviço de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="444a8-638">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="444a8-639">O processo do aplicativo deve ter permissões de leitura e gravação no local de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="444a8-639">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="444a8-640">**Nunca conceda a permissão EXECUTE.**</span><span class="sxs-lookup"><span data-stu-id="444a8-640">**Never grant execute permission.**</span></span>

* <span data-ttu-id="444a8-641">Serviço de armazenamento de dados (por exemplo, [armazenamento de BLOBs do Azure](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="444a8-641">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="444a8-642">Os serviços geralmente oferecem escalabilidade e resiliência aprimoradas sobre soluções locais que geralmente estão sujeitas a pontos únicos de falha.</span><span class="sxs-lookup"><span data-stu-id="444a8-642">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="444a8-643">Os serviços são um custo potencialmente menor em cenários de infraestrutura de armazenamento grandes.</span><span class="sxs-lookup"><span data-stu-id="444a8-643">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="444a8-644">Para obter mais informações, consulte [início rápido: usar o .net para criar um blob no armazenamento de objetos](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="444a8-644">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="444a8-645">O tópico demonstra <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> , mas <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> pode ser usado para salvar um <xref:System.IO.FileStream> armazenamento de BLOBs ao trabalhar com um <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="444a8-645">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="444a8-646">Cenários de upload de arquivo</span><span class="sxs-lookup"><span data-stu-id="444a8-646">File upload scenarios</span></span>

<span data-ttu-id="444a8-647">Duas abordagens gerais para carregar arquivos são buffer e streaming.</span><span class="sxs-lookup"><span data-stu-id="444a8-647">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="444a8-648">**de resposta**</span><span class="sxs-lookup"><span data-stu-id="444a8-648">**Buffering**</span></span>

<span data-ttu-id="444a8-649">O arquivo inteiro é lido em um <xref:Microsoft.AspNetCore.Http.IFormFile> , que é uma representação em C# do arquivo usado para processar ou salvar o arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-649">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="444a8-650">Os recursos (disco, memória) usados por carregamentos de arquivos dependem do número e do tamanho de carregamentos de arquivos simultâneos.</span><span class="sxs-lookup"><span data-stu-id="444a8-650">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="444a8-651">Se um aplicativo tentar armazenar em buffer um número excessivo de carregamentos, o site falhará quando ficar sem memória ou espaço em disco.</span><span class="sxs-lookup"><span data-stu-id="444a8-651">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="444a8-652">Se o tamanho ou a frequência dos carregamentos de arquivos estiver esgotando os recursos do aplicativo, use o streaming.</span><span class="sxs-lookup"><span data-stu-id="444a8-652">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="444a8-653">Qualquer arquivo em buffer único que exceda 64 KB é movido da memória para um arquivo temporário no disco.</span><span class="sxs-lookup"><span data-stu-id="444a8-653">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="444a8-654">O armazenamento em buffer de arquivos pequenos é abordado nas seguintes seções deste tópico:</span><span class="sxs-lookup"><span data-stu-id="444a8-654">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="444a8-655">Armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="444a8-655">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="444a8-656">Banco de dados</span><span class="sxs-lookup"><span data-stu-id="444a8-656">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="444a8-657">**Streaming**</span><span class="sxs-lookup"><span data-stu-id="444a8-657">**Streaming**</span></span>

<span data-ttu-id="444a8-658">O arquivo é recebido de uma solicitação de várias partes e processada ou salva diretamente pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-658">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="444a8-659">O streaming não melhora significativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="444a8-659">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="444a8-660">O streaming reduz as demandas de memória ou espaço em disco ao carregar arquivos.</span><span class="sxs-lookup"><span data-stu-id="444a8-660">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="444a8-661">O streaming de arquivos grandes é abordado na seção [carregar arquivos grandes com streaming](#upload-large-files-with-streaming) .</span><span class="sxs-lookup"><span data-stu-id="444a8-661">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="444a8-662">Carregar arquivos pequenos com associação de modelo em buffer para armazenamento físico</span><span class="sxs-lookup"><span data-stu-id="444a8-662">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="444a8-663">Para carregar arquivos pequenos, use um formulário com diversas partes ou Construa uma solicitação POST usando JavaScript.</span><span class="sxs-lookup"><span data-stu-id="444a8-663">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="444a8-664">O exemplo a seguir demonstra o uso de um Razor formulário de páginas para carregar um único arquivo (*pages/BufferedSingleFileUploadPhysical. cshtml* no aplicativo de exemplo):</span><span class="sxs-lookup"><span data-stu-id="444a8-664">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="444a8-665">O exemplo a seguir é análogo ao exemplo anterior, exceto pelo seguinte:</span><span class="sxs-lookup"><span data-stu-id="444a8-665">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="444a8-666">O JavaScript ([buscar API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) é usado para enviar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="444a8-666">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="444a8-667">Não há validação.</span><span class="sxs-lookup"><span data-stu-id="444a8-667">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="444a8-668">Para executar a POSTAgem de formulário no JavaScript para clientes que [não dão suporte à API de busca](https://caniuse.com/#feat=fetch), use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="444a8-668">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="444a8-669">Use um polifill de busca (por exemplo, [janela. FETCH retroativo (github/FETCH)](https://github.com/github/fetch)).</span><span class="sxs-lookup"><span data-stu-id="444a8-669">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="444a8-670">Use `XMLHttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="444a8-670">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="444a8-671">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="444a8-671">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="444a8-672">Para dar suporte a uploads de arquivo, os formulários HTML devem especificar um tipo de codificação ( `enctype` ) de `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="444a8-672">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="444a8-673">Para um `files` elemento input oferecer suporte ao carregamento de vários arquivos, forneça o `multiple` atributo no `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="444a8-673">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="444a8-674">Os arquivos individuais carregados no servidor podem ser acessados por meio de [Associação de modelo](xref:mvc/models/model-binding) usando <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="444a8-674">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="444a8-675">O aplicativo de exemplo demonstra vários uploads de arquivo em buffer para cenários de armazenamento físico e banco de dados.</span><span class="sxs-lookup"><span data-stu-id="444a8-675">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="444a8-676">**Não** use a `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> outros para exibição e registro em log.</span><span class="sxs-lookup"><span data-stu-id="444a8-676">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="444a8-677">Ao exibir ou registrar em log, o HTML codifica o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-677">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="444a8-678">Um invasor pode fornecer um nome de arquivo mal-intencionado, incluindo caminhos completos ou caminhos relativos.</span><span class="sxs-lookup"><span data-stu-id="444a8-678">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="444a8-679">Os aplicativos devem:</span><span class="sxs-lookup"><span data-stu-id="444a8-679">Applications should:</span></span>
>
> * <span data-ttu-id="444a8-680">Remova o caminho do nome de arquivo fornecido pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="444a8-680">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="444a8-681">Salve o nome de arquivo com codificação em HTML e removido por caminho para a interface do usuário ou log.</span><span class="sxs-lookup"><span data-stu-id="444a8-681">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="444a8-682">Gere um novo nome de arquivo aleatório para armazenamento.</span><span class="sxs-lookup"><span data-stu-id="444a8-682">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="444a8-683">O código a seguir remove o caminho do nome do arquivo:</span><span class="sxs-lookup"><span data-stu-id="444a8-683">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="444a8-684">Os exemplos fornecidos até o momento não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="444a8-684">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="444a8-685">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-685">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="444a8-686">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="444a8-686">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="444a8-687">Validação</span><span class="sxs-lookup"><span data-stu-id="444a8-687">Validation</span></span>](#validation)

<span data-ttu-id="444a8-688">Ao carregar arquivos usando a associação de modelo e <xref:Microsoft.AspNetCore.Http.IFormFile> o método de ação pode aceitar:</span><span class="sxs-lookup"><span data-stu-id="444a8-688">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="444a8-689">Um único <xref:Microsoft.AspNetCore.Http.IFormFile> .</span><span class="sxs-lookup"><span data-stu-id="444a8-689">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="444a8-690">Qualquer uma das seguintes coleções que representam vários arquivos:</span><span class="sxs-lookup"><span data-stu-id="444a8-690">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="444a8-691">[Lista](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="444a8-691">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="444a8-692">A Associação corresponde aos arquivos de formulário por nome.</span><span class="sxs-lookup"><span data-stu-id="444a8-692">Binding matches form files by name.</span></span> <span data-ttu-id="444a8-693">Por exemplo, o `name` valor HTML in `<input type="file" name="formFile">` deve corresponder ao parâmetro/Propriedade do C# associado ( `FormFile` ).</span><span class="sxs-lookup"><span data-stu-id="444a8-693">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="444a8-694">Para obter mais informações, consulte o [valor do atributo Match Name para o nome do parâmetro da seção do método post](#match-name-attribute-value-to-parameter-name-of-post-method) .</span><span class="sxs-lookup"><span data-stu-id="444a8-694">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="444a8-695">O exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-695">The following example:</span></span>

* <span data-ttu-id="444a8-696">Executa um loop em um ou mais arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-696">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="444a8-697">Usa [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para retornar um caminho completo para um arquivo, incluindo o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-697">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="444a8-698">Salva os arquivos no sistema de arquivos local usando um nome de arquivo gerado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-698">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="444a8-699">Retorna o número total e o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-699">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

<span data-ttu-id="444a8-700">Use `Path.GetRandomFileName` para gerar um nome de arquivo sem um caminho.</span><span class="sxs-lookup"><span data-stu-id="444a8-700">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="444a8-701">No exemplo a seguir, o caminho é obtido da configuração:</span><span class="sxs-lookup"><span data-stu-id="444a8-701">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="444a8-702">O caminho passado para o <xref:System.IO.FileStream> *deve* incluir o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-702">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="444a8-703">Se o nome do arquivo não for fornecido, um <xref:System.UnauthorizedAccessException> será lançado no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="444a8-703">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="444a8-704">Os arquivos carregados usando a <xref:Microsoft.AspNetCore.Http.IFormFile> técnica são armazenados em buffer na memória ou no disco no servidor antes do processamento.</span><span class="sxs-lookup"><span data-stu-id="444a8-704">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="444a8-705">Dentro do método de ação, o <xref:Microsoft.AspNetCore.Http.IFormFile> conteúdo é acessível como um <xref:System.IO.Stream> .</span><span class="sxs-lookup"><span data-stu-id="444a8-705">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="444a8-706">Além do sistema de arquivos local, os arquivos podem ser salvos em um compartilhamento de rede ou em um serviço de armazenamento de arquivos, como o [armazenamento de BLOBs do Azure](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span><span class="sxs-lookup"><span data-stu-id="444a8-706">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="444a8-707">Para outro exemplo que executa um loop sobre vários arquivos para carregar e usa nomes de arquivo seguros, consulte *pages/BufferedMultipleFileUploadPhysical. cshtml. cs* no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="444a8-707">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="444a8-708">[Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) gera um <xref:System.IO.IOException> se mais de 65.535 arquivos forem criados sem excluir arquivos temporários anteriores.</span><span class="sxs-lookup"><span data-stu-id="444a8-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="444a8-709">O limite de 65.535 arquivos é um limite por servidor.</span><span class="sxs-lookup"><span data-stu-id="444a8-709">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="444a8-710">Para obter mais informações sobre esse limite no sistema operacional Windows, consulte os comentários nos tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-710">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="444a8-711">Função GetTempFileNameA</span><span class="sxs-lookup"><span data-stu-id="444a8-711">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="444a8-712">Carregar arquivos pequenos com associação de modelo em buffer para um banco de dados</span><span class="sxs-lookup"><span data-stu-id="444a8-712">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="444a8-713">Para armazenar dados de arquivo binário em um banco de dado usando [Entity Framework](/ef/core/index), defina uma <xref:System.Byte> propriedade de matriz na entidade:</span><span class="sxs-lookup"><span data-stu-id="444a8-713">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="444a8-714">Especifique uma propriedade de modelo de página para a classe que inclui um <xref:Microsoft.AspNetCore.Http.IFormFile> :</span><span class="sxs-lookup"><span data-stu-id="444a8-714">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="444a8-715"><xref:Microsoft.AspNetCore.Http.IFormFile> pode ser usado diretamente como um parâmetro de método de ação ou como uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="444a8-715"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="444a8-716">O exemplo anterior usa uma propriedade de modelo associada.</span><span class="sxs-lookup"><span data-stu-id="444a8-716">The prior example uses a bound model property.</span></span>

<span data-ttu-id="444a8-717">O `FileUpload` é usado no Razor formulário páginas:</span><span class="sxs-lookup"><span data-stu-id="444a8-717">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="444a8-718">Quando o formulário for Postado no servidor, copie o <xref:Microsoft.AspNetCore.Http.IFormFile> para um fluxo e salve-o como uma matriz de bytes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="444a8-718">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="444a8-719">No exemplo a seguir, `_dbContext` armazena o contexto do banco de dados do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="444a8-719">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="444a8-720">O exemplo anterior é semelhante a um cenário demonstrado no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="444a8-720">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="444a8-721">*Páginas/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="444a8-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="444a8-722">*Pages/BufferedSingleFileUploadDb. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="444a8-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="444a8-723">Tenha cuidado ao armazenar dados binários em bancos de dados relacionais, pois isso pode afetar negativamente o desempenho.</span><span class="sxs-lookup"><span data-stu-id="444a8-723">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="444a8-724">Não confie ou confie na `FileName` propriedade de <xref:Microsoft.AspNetCore.Http.IFormFile> sem validação.</span><span class="sxs-lookup"><span data-stu-id="444a8-724">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="444a8-725">A `FileName` propriedade só deve ser usada para fins de exibição e somente após a codificação HTML.</span><span class="sxs-lookup"><span data-stu-id="444a8-725">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="444a8-726">Os exemplos fornecidos não levam em consideração as considerações de segurança.</span><span class="sxs-lookup"><span data-stu-id="444a8-726">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="444a8-727">Informações adicionais são fornecidas pelas seções e pelo aplicativo de [exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-727">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="444a8-728">Considerações sobre segurança</span><span class="sxs-lookup"><span data-stu-id="444a8-728">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="444a8-729">Validação</span><span class="sxs-lookup"><span data-stu-id="444a8-729">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="444a8-730">Carregar arquivos grandes com streaming</span><span class="sxs-lookup"><span data-stu-id="444a8-730">Upload large files with streaming</span></span>

<span data-ttu-id="444a8-731">O exemplo a seguir demonstra como usar o JavaScript para transmitir um arquivo a uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="444a8-731">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="444a8-732">O token antifalsificação do arquivo é gerado usando um atributo de filtro personalizado e passado para os cabeçalhos HTTP do cliente em vez de no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="444a8-732">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="444a8-733">Como o método de ação processa diretamente os dados carregados, a associação de modelo de formulário é desabilitada por outro filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="444a8-733">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="444a8-734">Dentro da ação, o conteúdo do formulário é lido usando um `MultipartReader`, que lê cada `MultipartSection` individual, processando o arquivo ou armazenando o conteúdo conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="444a8-734">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="444a8-735">Depois que as seções com várias partes forem lidas, a ação executará sua própria associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="444a8-735">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="444a8-736">A resposta de página inicial carrega o formulário e salva um token de antifalsificação em um cookie (por meio do `GenerateAntiforgeryTokenCookieAttribute` atributo).</span><span class="sxs-lookup"><span data-stu-id="444a8-736">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="444a8-737">O atributo usa o [suporte de antifalsificação](xref:security/anti-request-forgery) interno do ASP.NET Core para definir um cookie com um token de solicitação:</span><span class="sxs-lookup"><span data-stu-id="444a8-737">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="444a8-738">O `DisableFormValueModelBindingAttribute` é usado para desabilitar a associação de modelo:</span><span class="sxs-lookup"><span data-stu-id="444a8-738">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="444a8-739">No aplicativo de exemplo, `GenerateAntiforgeryTokenCookieAttribute` e `DisableFormValueModelBindingAttribute` são aplicados como filtros aos modelos de aplicativo de página do `/StreamedSingleFileUploadDb` e `/StreamedSingleFileUploadPhysical` no `Startup.ConfigureServices` usando convenções de [ Razor páginas](xref:razor-pages/razor-pages-conventions):</span><span class="sxs-lookup"><span data-stu-id="444a8-739">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="444a8-740">Como a associação de modelo não lê o formulário, os parâmetros que estão associados do formulário não são associados (a consulta, a rota e o cabeçalho continuam a funcionar).</span><span class="sxs-lookup"><span data-stu-id="444a8-740">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="444a8-741">O método de ação funciona diretamente com a `Request` propriedade.</span><span class="sxs-lookup"><span data-stu-id="444a8-741">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="444a8-742">Um `MultipartReader` é usado para ler cada seção.</span><span class="sxs-lookup"><span data-stu-id="444a8-742">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="444a8-743">Os dados de chave/valor são armazenados em um `KeyValueAccumulator` .</span><span class="sxs-lookup"><span data-stu-id="444a8-743">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="444a8-744">Depois que as seções com várias partes forem lidas, o conteúdo do `KeyValueAccumulator` será usado para associar os dados do formulário a um tipo de modelo.</span><span class="sxs-lookup"><span data-stu-id="444a8-744">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="444a8-745">O `StreamingController.UploadDatabase` método completo para streaming para um banco de dados com EF Core:</span><span class="sxs-lookup"><span data-stu-id="444a8-745">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="444a8-746">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper. cs*):</span><span class="sxs-lookup"><span data-stu-id="444a8-746">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="444a8-747">O `StreamingController.UploadPhysical` método completo para streaming para um local físico:</span><span class="sxs-lookup"><span data-stu-id="444a8-747">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="444a8-748">No aplicativo de exemplo, as verificações de validação são tratadas pelo `FileHelpers.ProcessStreamedFile` .</span><span class="sxs-lookup"><span data-stu-id="444a8-748">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="444a8-749">Validação</span><span class="sxs-lookup"><span data-stu-id="444a8-749">Validation</span></span>

<span data-ttu-id="444a8-750">A classe do aplicativo de exemplo `FileHelpers` demonstra várias verificações para <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivos em buffer e em fluxo.</span><span class="sxs-lookup"><span data-stu-id="444a8-750">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="444a8-751">Para processar <xref:Microsoft.AspNetCore.Http.IFormFile> carregamentos de arquivo em buffer no aplicativo de exemplo, consulte o `ProcessFormFile` método no arquivo *Utilities/FileHelpers. cs* .</span><span class="sxs-lookup"><span data-stu-id="444a8-751">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="444a8-752">Para o processamento de arquivos transmitidos, consulte o `ProcessStreamedFile` método no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-752">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="444a8-753">Os métodos de processamento de validação demonstrados no aplicativo de exemplo não verificam o conteúdo dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-753">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="444a8-754">Na maioria dos cenários de produção, uma API de scanner de vírus/malware é usada no arquivo antes de disponibilizar o arquivo para os usuários ou outros sistemas.</span><span class="sxs-lookup"><span data-stu-id="444a8-754">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="444a8-755">Embora o tópico exemplo forneça um exemplo de trabalho de técnicas de validação, não implemente a `FileHelpers` classe em um aplicativo de produção, a menos que você:</span><span class="sxs-lookup"><span data-stu-id="444a8-755">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="444a8-756">Entenda totalmente a implementação.</span><span class="sxs-lookup"><span data-stu-id="444a8-756">Fully understand the implementation.</span></span>
> * <span data-ttu-id="444a8-757">Modifique a implementação conforme apropriado para o ambiente e as especificações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-757">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="444a8-758">**Nunca implemente indiscriminadamente o código de segurança em um aplicativo sem atender a esses requisitos.**</span><span class="sxs-lookup"><span data-stu-id="444a8-758">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="444a8-759">Validação de conteúdo</span><span class="sxs-lookup"><span data-stu-id="444a8-759">Content validation</span></span>

<span data-ttu-id="444a8-760">**Use uma API de verificação de vírus/malware de terceiros no conteúdo carregado.**</span><span class="sxs-lookup"><span data-stu-id="444a8-760">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="444a8-761">A verificação de arquivos exige muitos recursos do servidor em cenários de alto volume.</span><span class="sxs-lookup"><span data-stu-id="444a8-761">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="444a8-762">Se o desempenho do processamento de solicitações for reduzido devido à verificação de arquivo, considere descarregar o trabalho de verificação para um [serviço em segundo plano](xref:fundamentals/host/hosted-services), possivelmente um serviço em execução em um servidor diferente do servidor do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-762">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="444a8-763">Normalmente, os arquivos carregados são mantidos em uma área em quarentena até que o verificador de vírus em segundo plano os Verifique.</span><span class="sxs-lookup"><span data-stu-id="444a8-763">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="444a8-764">Quando um arquivo passa, o arquivo é movido para o local de armazenamento de arquivo normal.</span><span class="sxs-lookup"><span data-stu-id="444a8-764">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="444a8-765">Essas etapas geralmente são executadas em conjunto com um registro de banco de dados que indica o status de verificação de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-765">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="444a8-766">Ao usar essa abordagem, o aplicativo e o servidor de aplicativos permanecem concentrados em responder às solicitações.</span><span class="sxs-lookup"><span data-stu-id="444a8-766">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="444a8-767">Validação de extensão de arquivo</span><span class="sxs-lookup"><span data-stu-id="444a8-767">File extension validation</span></span>

<span data-ttu-id="444a8-768">A extensão do arquivo carregado deve ser verificada em uma lista de extensões permitidas.</span><span class="sxs-lookup"><span data-stu-id="444a8-768">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="444a8-769">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="444a8-769">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="444a8-770">Validação de assinatura de arquivo</span><span class="sxs-lookup"><span data-stu-id="444a8-770">File signature validation</span></span>

<span data-ttu-id="444a8-771">A assinatura de um arquivo é determinada pelos primeiros bytes no início de um arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-771">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="444a8-772">Esses bytes podem ser usados para indicar se a extensão corresponde ao conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-772">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="444a8-773">O aplicativo de exemplo verifica as assinaturas de arquivo para alguns tipos de arquivo comuns.</span><span class="sxs-lookup"><span data-stu-id="444a8-773">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="444a8-774">No exemplo a seguir, a assinatura de arquivo para uma imagem JPEG é verificada em relação ao arquivo:</span><span class="sxs-lookup"><span data-stu-id="444a8-774">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="444a8-775">Para obter assinaturas de arquivo adicionais, consulte o [banco de dados assinaturas de arquivo](https://www.filesignatures.net/) e especificações de arquivo oficial.</span><span class="sxs-lookup"><span data-stu-id="444a8-775">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="444a8-776">Segurança de nome de arquivo</span><span class="sxs-lookup"><span data-stu-id="444a8-776">File name security</span></span>

<span data-ttu-id="444a8-777">Nunca use um nome de arquivo fornecido pelo cliente para salvar um arquivo no armazenamento físico.</span><span class="sxs-lookup"><span data-stu-id="444a8-777">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="444a8-778">Crie um nome de arquivo seguro para o arquivo usando [Path. GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) ou [Path. GetTempFileName](xref:System.IO.Path.GetTempFileName*) para criar um caminho completo (incluindo o nome do arquivo) para armazenamento temporário.</span><span class="sxs-lookup"><span data-stu-id="444a8-778">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="444a8-779">Razor o HTML automaticamente codifica valores de propriedade para exibição.</span><span class="sxs-lookup"><span data-stu-id="444a8-779">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="444a8-780">O código a seguir é seguro para usar:</span><span class="sxs-lookup"><span data-stu-id="444a8-780">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="444a8-781">Fora do Razor , sempre <xref:System.Net.WebUtility.HtmlEncode*> nome do arquivo o conteúdo da solicitação de um usuário.</span><span class="sxs-lookup"><span data-stu-id="444a8-781">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="444a8-782">Muitas implementações devem incluir uma verificação de que o arquivo existe; caso contrário, o arquivo será substituído por um arquivo com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="444a8-782">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="444a8-783">Forneça lógica adicional para atender às especificações do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="444a8-783">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="444a8-784">Validação de tamanho</span><span class="sxs-lookup"><span data-stu-id="444a8-784">Size validation</span></span>

<span data-ttu-id="444a8-785">Limite o tamanho dos arquivos carregados.</span><span class="sxs-lookup"><span data-stu-id="444a8-785">Limit the size of uploaded files.</span></span>

<span data-ttu-id="444a8-786">No aplicativo de exemplo, o tamanho do arquivo é limitado a 2 MB (indicado em bytes).</span><span class="sxs-lookup"><span data-stu-id="444a8-786">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="444a8-787">O limite é fornecido por meio da [configuração](xref:fundamentals/configuration/index) do *appsettings.jsno* arquivo:</span><span class="sxs-lookup"><span data-stu-id="444a8-787">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="444a8-788">O `FileSizeLimit` é injetado em `PageModel` classes:</span><span class="sxs-lookup"><span data-stu-id="444a8-788">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="444a8-789">Quando um tamanho de arquivo exceder o limite, o arquivo será rejeitado:</span><span class="sxs-lookup"><span data-stu-id="444a8-789">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="444a8-790">Coincidir valor do atributo de nome com o nome do parâmetro do método POST</span><span class="sxs-lookup"><span data-stu-id="444a8-790">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="444a8-791">Em não Razor formulários que postam dados de formulário ou usam o JavaScript `FormData` diretamente, o nome especificado no elemento do formulário ou `FormData` deve corresponder ao nome do parâmetro na ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="444a8-791">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="444a8-792">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="444a8-792">In the following example:</span></span>

* <span data-ttu-id="444a8-793">Ao usar um `<input>` elemento, o `name` atributo é definido como o valor `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="444a8-793">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="444a8-794">Ao usar `FormData` em JavaScript, o nome é definido como o valor `battlePlans` :</span><span class="sxs-lookup"><span data-stu-id="444a8-794">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="444a8-795">Use um nome correspondente para o parâmetro do método C# ( `battlePlans` ):</span><span class="sxs-lookup"><span data-stu-id="444a8-795">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="444a8-796">Para um Razor método de manipulador de página de páginas chamado `Upload` :</span><span class="sxs-lookup"><span data-stu-id="444a8-796">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="444a8-797">Para um método de ação do controlador de POSTAgem MVC:</span><span class="sxs-lookup"><span data-stu-id="444a8-797">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="444a8-798">Configuração de servidor e aplicativo</span><span class="sxs-lookup"><span data-stu-id="444a8-798">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="444a8-799">Limite de comprimento de corpo com várias partes</span><span class="sxs-lookup"><span data-stu-id="444a8-799">Multipart body length limit</span></span>

<span data-ttu-id="444a8-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> define o limite para o comprimento de cada corpo com diversas partes.</span><span class="sxs-lookup"><span data-stu-id="444a8-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="444a8-801">As seções de formulário que excedem esse limite lançam um <xref:System.IO.InvalidDataException> quando analisado.</span><span class="sxs-lookup"><span data-stu-id="444a8-801">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="444a8-802">O padrão é 134.217.728 (128 MB).</span><span class="sxs-lookup"><span data-stu-id="444a8-802">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="444a8-803">Personalize o limite usando a <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> configuração em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="444a8-803">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="444a8-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> é usado para definir o <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="444a8-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="444a8-805">Em um Razor aplicativo de páginas, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="444a8-805">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="444a8-806">Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro ao modelo de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="444a8-806">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="444a8-807">Tamanho máximo do corpo de solicitação do Kestrel</span><span class="sxs-lookup"><span data-stu-id="444a8-807">Kestrel maximum request body size</span></span>

<span data-ttu-id="444a8-808">Para aplicativos hospedados pelo Kestrel, o tamanho máximo padrão do corpo da solicitação é 30 milhões bytes, que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="444a8-808">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="444a8-809">Personalize o limite usando a opção de servidor [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel:</span><span class="sxs-lookup"><span data-stu-id="444a8-809">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<span data-ttu-id="444a8-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> é usado para definir o [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) para uma única página ou ação.</span><span class="sxs-lookup"><span data-stu-id="444a8-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="444a8-811">Em um Razor aplicativo de páginas, aplique o filtro com uma [Convenção](xref:razor-pages/razor-pages-conventions) em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="444a8-811">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="444a8-812">Em um Razor aplicativo de páginas ou um aplicativo MVC, aplique o filtro à classe de manipulador de página ou ao método de ação:</span><span class="sxs-lookup"><span data-stu-id="444a8-812">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="444a8-813">Outros limites de Kestrel</span><span class="sxs-lookup"><span data-stu-id="444a8-813">Other Kestrel limits</span></span>

<span data-ttu-id="444a8-814">Outros limites de Kestrel podem ser aplicados para aplicativos hospedados pelo Kestrel:</span><span class="sxs-lookup"><span data-stu-id="444a8-814">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="444a8-815">Número máximo de conexões de cliente</span><span class="sxs-lookup"><span data-stu-id="444a8-815">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="444a8-816">Taxas de dados de solicitação e resposta</span><span class="sxs-lookup"><span data-stu-id="444a8-816">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="444a8-817">IIS</span><span class="sxs-lookup"><span data-stu-id="444a8-817">IIS</span></span>

<span data-ttu-id="444a8-818">O limite de solicitação padrão ( `maxAllowedContentLength` ) é 30 milhões bytes, que é aproximadamente 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="444a8-818">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="444a8-819">Personalize o limite no `web.config` arquivo.</span><span class="sxs-lookup"><span data-stu-id="444a8-819">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="444a8-820">No exemplo a seguir, o limite é definido como 50 MB (52.428.800 bytes):</span><span class="sxs-lookup"><span data-stu-id="444a8-820">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="444a8-821">A `maxAllowedContentLength` configuração se aplica somente ao IIS.</span><span class="sxs-lookup"><span data-stu-id="444a8-821">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="444a8-822">Para obter mais informações, consulte [limites `<requestLimits>` de solicitação ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span><span class="sxs-lookup"><span data-stu-id="444a8-822">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="444a8-823">Aumente o tamanho máximo do corpo de solicitação para a solicitação HTTP definindo <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="444a8-823">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="444a8-824">No exemplo a seguir, o limite é definido como 50 MB (52.428.800 bytes):</span><span class="sxs-lookup"><span data-stu-id="444a8-824">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="444a8-825">Para obter mais informações, consulte <xref:host-and-deploy/iis/index#iis-options>.</span><span class="sxs-lookup"><span data-stu-id="444a8-825">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="444a8-826">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="444a8-826">Troubleshoot</span></span>

<span data-ttu-id="444a8-827">Abaixo, são listados alguns problemas comuns encontrados ao trabalhar com o upload de arquivos e suas possíveis soluções.</span><span class="sxs-lookup"><span data-stu-id="444a8-827">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="444a8-828">Erro não encontrado quando implantado em um servidor IIS</span><span class="sxs-lookup"><span data-stu-id="444a8-828">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="444a8-829">O erro a seguir indica que o arquivo carregado excede o tamanho do conteúdo configurado do servidor:</span><span class="sxs-lookup"><span data-stu-id="444a8-829">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="444a8-830">Para obter mais informações, consulte a seção [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="444a8-830">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="444a8-831">Falha de conexão</span><span class="sxs-lookup"><span data-stu-id="444a8-831">Connection failure</span></span>

<span data-ttu-id="444a8-832">Um erro de conexão e uma conexão de servidor de redefinição provavelmente indicam que o arquivo carregado excede o tamanho máximo do corpo de solicitação do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="444a8-832">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="444a8-833">Para obter mais informações, consulte a seção [tamanho máximo de corpo de solicitação do Kestrel](#kestrel-maximum-request-body-size) .</span><span class="sxs-lookup"><span data-stu-id="444a8-833">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="444a8-834">Os limites de conexão de cliente Kestrel também podem exigir ajuste.</span><span class="sxs-lookup"><span data-stu-id="444a8-834">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="444a8-835">Exceção de referência nula com IFormFile</span><span class="sxs-lookup"><span data-stu-id="444a8-835">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="444a8-836">Se o controlador estiver aceitando arquivos carregados usando <xref:Microsoft.AspNetCore.Http.IFormFile> , mas o valor for `null` , confirme se o formulário HTML está especificando um `enctype` valor de `multipart/form-data` .</span><span class="sxs-lookup"><span data-stu-id="444a8-836">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="444a8-837">Se esse atributo não estiver definido no `<form>` elemento, o upload do arquivo não ocorrerá e todos os <xref:Microsoft.AspNetCore.Http.IFormFile> argumentos associados serão `null` .</span><span class="sxs-lookup"><span data-stu-id="444a8-837">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="444a8-838">Além disso, confirme se a [nomenclatura de carregamento em dados de formulário corresponde à nomenclatura do aplicativo](#match-name-attribute-value-to-parameter-name-of-post-method).</span><span class="sxs-lookup"><span data-stu-id="444a8-838">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="444a8-839">O fluxo era muito longo</span><span class="sxs-lookup"><span data-stu-id="444a8-839">Stream was too long</span></span>

<span data-ttu-id="444a8-840">Os exemplos neste tópico dependem de <xref:System.IO.MemoryStream> manter o conteúdo do arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="444a8-840">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="444a8-841">O limite de tamanho de um `MemoryStream` é `int.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="444a8-841">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="444a8-842">Se o cenário de upload de arquivo do aplicativo exigir a retenção de conteúdo de arquivo com mais de 50 MB, use uma abordagem alternativa que não dependa de um único `MemoryStream` para manter o conteúdo de um arquivo carregado.</span><span class="sxs-lookup"><span data-stu-id="444a8-842">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="444a8-843">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="444a8-843">Additional resources</span></span>

* [<span data-ttu-id="444a8-844">Descarga de solicitação de conexão HTTP</span><span class="sxs-lookup"><span data-stu-id="444a8-844">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* <span data-ttu-id="444a8-845">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload) (Carregamento de arquivo irrestrito)</span><span class="sxs-lookup"><span data-stu-id="444a8-845">[Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)</span></span>
* [<span data-ttu-id="444a8-846">Segurança do Azure: estrutura de segurança: validação de entrada | Mitigações</span><span class="sxs-lookup"><span data-stu-id="444a8-846">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="444a8-847">Padrões de design de nuvem do Azure: padrão valet Key</span><span class="sxs-lookup"><span data-stu-id="444a8-847">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
