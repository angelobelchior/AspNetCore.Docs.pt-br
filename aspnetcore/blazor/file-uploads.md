---
title: ASP.NET Core Blazor carregamentos de arquivos
author: guardrex
description: Saiba como carregar arquivos no Blazor com o componente InputFile.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
no-loc:
- appsettings.json
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
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: ca49564136e030fdaf86eefac56146fcb79f7bad
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673946"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a>ASP.NET Core Blazor carregamentos de arquivos

Por [Daniel Roth](https://github.com/danroth27) e [Pranav Krishnamoorthy](https://github.com/pranavkm)

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([como baixar](xref:index#how-to-download-a-sample))

Use o `InputFile` componente para ler os dados do arquivo de navegador no código .net, incluindo para carregamentos de arquivos.

> [!WARNING]
> Sempre siga as práticas recomendadas de segurança de carregamento de arquivo. Para obter mais informações, consulte <xref:mvc/models/file-uploads#security-considerations>.

## <a name="inputfile-component"></a>componente `InputFile`

O `InputFile` componente é renderizado como uma entrada HTML do tipo `file` .

Por padrão, o usuário seleciona arquivos únicos. Adicione o `multiple` atributo para permitir que o usuário carregue vários arquivos ao mesmo tempo. Quando um ou mais arquivos é selecionado pelo usuário, o `InputFile` componente dispara um `OnChange` evento e passa um `InputFileChangeEventArgs` que fornece acesso à lista de arquivos selecionada e aos detalhes de cada arquivo.

Para ler dados de um arquivo selecionado pelo usuário:

* Chame `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` no arquivo e leia a partir do fluxo retornado. Para obter mais informações, consulte a seção [fluxos de arquivos](#file-streams) .
* O <xref:System.IO.Stream> retornado por `OpenReadStream` impõe um tamanho máximo em bytes do `Stream` que está sendo lido. Por padrão, os arquivos com tamanho superior a 512.000 bytes (500 KB) podem ser lidos antes que qualquer outra leitura resulte em uma exceção. Esse limite está presente para impedir que os desenvolvedores leiam acidentalmente arquivos grandes na memória. O `maxAllowedSize` parâmetro on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` pode ser usado para especificar um tamanho maior, se necessário.
* Evite ler o fluxo de arquivos de entrada diretamente na memória. Por exemplo, não copie bytes de arquivo em um <xref:System.IO.MemoryStream> ou leia como uma matriz de bytes. Essas abordagens podem resultar em problemas de desempenho e segurança, especialmente no Blazor Server . Em vez disso, considere copiar bytes de arquivo para um repositório externo, como um BLOB ou um arquivo no disco.

Um componente que recebe um arquivo de imagem pode chamar o `RequestImageFileAsync` método de conveniência no arquivo para redimensionar os dados da imagem dentro do tempo de execução do JavaScript do navegador antes que a imagem seja transmitida para o aplicativo.

O exemplo a seguir demonstra o carregamento de vários arquivos de imagem em um componente. `InputFileChangeEventArgs.GetMultipleFiles` permite ler vários arquivos. Especifique o número máximo de arquivos que você pretende ler para impedir que um usuário mal-intencionado carregue um número maior de arquivos do que o aplicativo espera. `InputFileChangeEventArgs.File` permite a leitura do primeiro e único arquivo se o upload do arquivo não oferecer suporte a vários arquivos.

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

`IBrowserFile` retorna [os metadados expostos pelo navegador](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) como propriedades. Esses metadados podem ser úteis para a validação preliminar. Por exemplo, consulte os [ `FileUpload.razor` `FilePreview.razor` componentes de exemplo e](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).

## <a name="file-streams"></a>Fluxos de arquivos

Em um Blazor WebAssembly aplicativo, os dados são transmitidos diretamente para o código .net dentro do navegador.

Em um Blazor Server aplicativo, os dados de arquivo são transmitidos pela SignalR conexão no código .net no servidor, pois o arquivo é lido no fluxo. [`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) permite configurar características de upload de arquivo para Blazor Server .

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
