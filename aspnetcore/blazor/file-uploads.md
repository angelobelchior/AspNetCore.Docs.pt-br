---
title: ASP.NET Core Blazor carregamentos de arquivos
author: guardrex
description: Saiba como carregar arquivos no Blazor com o componente InputFile.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
ms.openlocfilehash: de4654f2efc401143e066628b096052efa65d7a0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722966"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="e4752-103">ASP.NET Core Blazor carregamentos de arquivos</span><span class="sxs-lookup"><span data-stu-id="e4752-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="e4752-104">Por [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e4752-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="e4752-105">Use o `InputFile` componente para ler os dados do arquivo de navegador no código .net, incluindo para carregamentos de arquivos.</span><span class="sxs-lookup"><span data-stu-id="e4752-105">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span> <span data-ttu-id="e4752-106">O `InputFile` componente é renderizado como uma entrada HTML do tipo `file` .</span><span class="sxs-lookup"><span data-stu-id="e4752-106">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="e4752-107">Por padrão, o usuário seleciona arquivos únicos.</span><span class="sxs-lookup"><span data-stu-id="e4752-107">By default, the user selects single files.</span></span> <span data-ttu-id="e4752-108">Adicione o `multiple` atributo para permitir que o usuário carregue vários arquivos ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="e4752-108">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="e4752-109">Quando um ou mais arquivos é selecionado pelo usuário, o `InputFile` componente dispara um `OnChange` evento e passa um `InputFileChangeEventArgs` que fornece acesso à lista de arquivos selecionada e aos detalhes de cada arquivo.</span><span class="sxs-lookup"><span data-stu-id="e4752-109">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="e4752-110">Um componente que recebe um arquivo de imagem pode chamar o `RequestImageFileAsync` método de conveniência no arquivo para redimensionar os dados da imagem dentro do tempo de execução do JavaScript do navegador antes que a imagem seja transmitida para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e4752-110">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="e4752-111">O exemplo a seguir demonstra o carregamento de vários arquivos de imagem em um componente:</span><span class="sxs-lookup"><span data-stu-id="e4752-111">The following example demonstrates multiple image file upload in a component:</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h3>Images</h3>

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
        var imageFiles = e.GetMultipleFiles();
        var format = "image/png";

        foreach (var imageFile in imageFiles)
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

<span data-ttu-id="e4752-112">Para ler dados de um arquivo selecionado pelo usuário, chame `OpenReadStream` no arquivo e leia o fluxo retornado.</span><span class="sxs-lookup"><span data-stu-id="e4752-112">To read data from a user-selected file, call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="e4752-113">Em um Blazor WebAssembly aplicativo, os dados são transmitidos diretamente para o código .net dentro do navegador.</span><span class="sxs-lookup"><span data-stu-id="e4752-113">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span> <span data-ttu-id="e4752-114">Em um Blazor Server aplicativo, os dados de arquivo são transmitidos para o código .net no servidor, pois o arquivo é lido no fluxo.</span><span class="sxs-lookup"><span data-stu-id="e4752-114">In a Blazor Server app, the file data is streamed into .NET code on the server as the file is read from the stream.</span></span> 
