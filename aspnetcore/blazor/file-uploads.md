---
title: ASP.NET Core Blazor carregamentos de arquivos
author: guardrex
description: Saiba como carregar arquivos no Blazor com o componente InputFile.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
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
uid: blazor/file-uploads
ms.openlocfilehash: 06d1464cb731a8008362fc911f463e4ff8a37b6b
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606661"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="6d9e1-103">ASP.NET Core Blazor carregamentos de arquivos</span><span class="sxs-lookup"><span data-stu-id="6d9e1-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="6d9e1-104">Por [Daniel Roth](https://github.com/danroth27) e [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="6d9e1-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="6d9e1-105">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6d9e1-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6d9e1-106">Use o `InputFile` componente para ler os dados do arquivo de navegador no código .net, incluindo para carregamentos de arquivos.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="6d9e1-107">Sempre siga as práticas recomendadas de segurança de carregamento de arquivo.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="6d9e1-108">Para obter mais informações, consulte <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="6d9e1-109">componente `InputFile`</span><span class="sxs-lookup"><span data-stu-id="6d9e1-109">`InputFile` component</span></span>

<span data-ttu-id="6d9e1-110">O `InputFile` componente é renderizado como uma entrada HTML do tipo `file` .</span><span class="sxs-lookup"><span data-stu-id="6d9e1-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="6d9e1-111">Por padrão, o usuário seleciona arquivos únicos.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-111">By default, the user selects single files.</span></span> <span data-ttu-id="6d9e1-112">Adicione o `multiple` atributo para permitir que o usuário carregue vários arquivos ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="6d9e1-113">Quando um ou mais arquivos é selecionado pelo usuário, o `InputFile` componente dispara um `OnChange` evento e passa um `InputFileChangeEventArgs` que fornece acesso à lista de arquivos selecionada e aos detalhes de cada arquivo.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="6d9e1-114">Para ler dados de um arquivo selecionado pelo usuário:</span><span class="sxs-lookup"><span data-stu-id="6d9e1-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="6d9e1-115">Chame `OpenReadStream` no arquivo e leia a partir do fluxo retornado.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-115">Call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="6d9e1-116">Para obter mais informações, consulte a seção [fluxos de arquivos](#file-streams) .</span><span class="sxs-lookup"><span data-stu-id="6d9e1-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="6d9e1-117">Use `ReadAsync`.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-117">Use `ReadAsync`.</span></span> <span data-ttu-id="6d9e1-118">Por padrão, `ReadAsync` o tamanho permite a leitura de um arquivo menor que 524.288 KB (512 KB).</span><span class="sxs-lookup"><span data-stu-id="6d9e1-118">By default, `ReadAsync` only allows reading a file smaller than 524,288 KB (512 KB) in size.</span></span> <span data-ttu-id="6d9e1-119">Esse limite está presente para impedir que os desenvolvedores leiam acidentalmente arquivos grandes na memória.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="6d9e1-120">Especifique uma aproximação razoável para o tamanho máximo de arquivo esperado se arquivos maiores tiverem suporte.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-120">Specify a reasonable approximation for the maximum expected file size if larger files must be supported.</span></span> <span data-ttu-id="6d9e1-121">Evite ler o fluxo de arquivos de entrada diretamente na memória.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="6d9e1-122">Por exemplo, não copie bytes de arquivo em um <xref:System.IO.MemoryStream> ou leia como uma matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="6d9e1-123">Essas abordagens podem resultar em problemas de desempenho e segurança, especialmente no Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="6d9e1-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="6d9e1-124">Em vez disso, considere copiar bytes de arquivo para um repositório externo, como um BLOB ou um arquivo no disco.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="6d9e1-125">Um componente que recebe um arquivo de imagem pode chamar o `RequestImageFileAsync` método de conveniência no arquivo para redimensionar os dados da imagem dentro do tempo de execução do JavaScript do navegador antes que a imagem seja transmitida para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="6d9e1-126">O exemplo a seguir demonstra o carregamento de vários arquivos de imagem em um componente.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="6d9e1-127">`InputFileChangeEventArgs.GetMultipleFiles` permite ler vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="6d9e1-128">Especifique o número máximo de arquivos que você pretende ler para impedir que um usuário mal-intencionado carregue um número maior de arquivos do que o aplicativo espera.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="6d9e1-129">`InputFileChangeEventArgs.File` permite a leitura do primeiro e único arquivo se o upload do arquivo não oferecer suporte a vários arquivos.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

<span data-ttu-id="6d9e1-130">`IBrowserFile` retorna [os metadados expostos pelo navegador](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) como propriedades.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="6d9e1-131">Esses metadados podem ser úteis para a validação preliminar.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="6d9e1-132">Por exemplo, consulte os [ `FileUpload.razor` `FilePreview.razor` componentes de exemplo e](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="6d9e1-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="6d9e1-133">Fluxos de arquivos</span><span class="sxs-lookup"><span data-stu-id="6d9e1-133">File streams</span></span>

<span data-ttu-id="6d9e1-134">Em um Blazor WebAssembly aplicativo, os dados são transmitidos diretamente para o código .net dentro do navegador.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-134">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="6d9e1-135">Em um Blazor Server aplicativo, os dados de arquivo são transmitidos pela SignalR conexão no código .net no servidor, pois o arquivo é lido no fluxo.</span><span class="sxs-lookup"><span data-stu-id="6d9e1-135">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="6d9e1-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) permite configurar características de upload de arquivo para Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="6d9e1-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6d9e1-137">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6d9e1-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
