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
# <a name="aspnet-core-no-locblazor-file-uploads"></a>ASP.NET Core Blazor carregamentos de arquivos

Por [Daniel Roth](https://github.com/danroth27)

Use o `InputFile` componente para ler os dados do arquivo de navegador no código .net, incluindo para carregamentos de arquivos. O `InputFile` componente é renderizado como uma entrada HTML do tipo `file` .

Por padrão, o usuário seleciona arquivos únicos. Adicione o `multiple` atributo para permitir que o usuário carregue vários arquivos ao mesmo tempo. Quando um ou mais arquivos é selecionado pelo usuário, o `InputFile` componente dispara um `OnChange` evento e passa um `InputFileChangeEventArgs` que fornece acesso à lista de arquivos selecionada e aos detalhes de cada arquivo.

Um componente que recebe um arquivo de imagem pode chamar o `RequestImageFileAsync` método de conveniência no arquivo para redimensionar os dados da imagem dentro do tempo de execução do JavaScript do navegador antes que a imagem seja transmitida para o aplicativo.

O exemplo a seguir demonstra o carregamento de vários arquivos de imagem em um componente:

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

Para ler dados de um arquivo selecionado pelo usuário, chame `OpenReadStream` no arquivo e leia o fluxo retornado. Em um Blazor WebAssembly aplicativo, os dados são transmitidos diretamente para o código .net dentro do navegador. Em um Blazor Server aplicativo, os dados de arquivo são transmitidos para o código .net no servidor, pois o arquivo é lido no fluxo. 
