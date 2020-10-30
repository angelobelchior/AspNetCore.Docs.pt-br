---
title: BlazorIsolamento ASP.NET Core CSS
author: daveabrock
description: Saiba como o isolamento CSS permite que você alcance o CSS para seus componentes, o que pode simplificar seu CSS e evitar colisões com outros componentes ou bibliotecas.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 628e7dc897912beaae0df792b82958517ac70ca4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056316"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a>BlazorIsolamento ASP.NET Core CSS

Por [Dave Brock](https://twitter.com/daveabrock)

O isolamento CSS simplifica a superfície CSS de um aplicativo, impedindo dependências em estilos globais e ajuda a evitar conflitos de estilo entre componentes e bibliotecas.

## <a name="enable-css-isolation"></a>Habilitar isolamento de CSS 

Para definir estilos específicos do componente, crie um `razor.css` arquivo correspondente ao nome do `.razor` arquivo para o componente. Este `razor.css` arquivo é um *arquivo CSS com escopo definido* . 

Para um `MyComponent` componente que tenha um `MyComponent.razor` arquivo, crie um arquivo junto com o componente chamado `MyComponent.razor.css` . O `MyComponent` valor no `razor.css` nome do arquivo **não** diferencia maiúsculas de minúsculas.

Por exemplo, para adicionar o isolamento de CSS ao `Counter` componente no Blazor modelo de projeto padrão, adicione um novo arquivo chamado `Counter.razor.css` junto com o `Counter.razor` arquivo e, em seguida, adicione o seguinte CSS:

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

Os estilos definidos em `Counter.razor.css` são aplicados somente à saída renderizada do `Counter` componente. Quaisquer `h1` declarações CSS definidas em outro lugar no aplicativo não entram em conflito com os `Counter` estilos.

> [!NOTE]
> Para garantir o isolamento de estilo quando o agrupamento ocorre, `@import` Razor os blocos não têm suporte com arquivos CSS com escopo definido.

## <a name="css-isolation-bundling"></a>Agrupamento de isolamento de CSS

O isolamento de CSS ocorre no momento da compilação. Durante esse processo, o Blazor reescreve seletores CSS para corresponder à marcação renderizada pelo componente. Esses estilos CSS reescritos são agrupados e produzidos como um ativo estático em `{PROJECT NAME}.styles.css` , em que o espaço reservado `{PROJECT NAME}` é o nome do produto ou do pacote referenciado.

Por padrão, esses arquivos estáticos são referenciados no caminho raiz do aplicativo. No aplicativo, referencie o arquivo agrupado inspecionando a referência dentro da `<head>` marca do HTML gerado:

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

No arquivo empacotado, cada componente é associado a um identificador de escopo. Para cada componente com estilo, um atributo HTML é anexado com o formato `b-<10-character-string>` . O identificador é exclusivo e diferente para cada aplicativo. No componente renderizado `Counter` , Blazor acrescenta um identificador de escopo ao `h1` elemento:

```html
<h1 b-3xxtam6d07>
```

O `MyProjectName.styles.css` arquivo usa o identificador de escopo para agrupar uma declaração de estilo com seu componente. O exemplo a seguir fornece o estilo para o `<h1>` elemento anterior:

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

No momento da compilação, um pacote de projeto é criado com a Convenção `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` , em que o espaço reservado `{STATIC WEB ASSETS BASE PATH}` é o caminho base dos ativos da Web estáticos.

Se outros projetos forem utilizados, como pacotes NuGet ou bibliotecas de [ Razor classes](xref:blazor/components/class-libraries), o arquivo agrupado:

* Faz referência aos estilos usando importações CSS.
* Não é publicado como um ativo da Web estático do aplicativo que consome os estilos.

## <a name="child-component-support"></a>Suporte a componentes filho

Por padrão, o isolamento de CSS só se aplica ao componente que você associa ao formato `{COMPONENT NAME}.razor.css` , em que o espaço reservado `{COMPONENT NAME}` é geralmente o nome do componente. Para aplicar as alterações a um componente filho, use o `::deep` combinador para quaisquer elementos descendentes no arquivo do componente pai `razor.css` . O `::deep` combinador seleciona elementos que são *descendentes* do identificador de escopo gerado de um elemento. 

O exemplo a seguir mostra um componente pai chamado `Parent` com um componente filho chamado `Child` .

`Parent.razor`:

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

`Child.razor`:

```razor
<h1>Child Component</h1>
```

Atualize a `h1` declaração em `Parent.razor.css` com o `::deep` combinador para significar que a `h1` declaração de estilo deve ser aplicada ao componente pai e a seus filhos:

```css
::deep h1 { 
    color: red;
}
```

O `h1` estilo agora se aplica aos `Parent` `Child` componentes e sem a necessidade de criar um arquivo CSS com escopo separado para o componente filho.

> [!NOTE]
> O `::deep` combinador funciona apenas com elementos descendentes. A seguinte estrutura HTML aplica os `h1` estilos aos componentes conforme esperado:
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> Nesse cenário, ASP.NET Core aplica o identificador de escopo do componente pai ao `div` elemento, portanto, o navegador sabe herdar estilos do componente pai.
>
> No entanto, a exclusão do `div` elemento remove a relação descendente e o estilo **não** é aplicado ao componente filho:
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a>Suporte ao pré-processador do CSS

Os pré-processadores CSS são úteis para melhorar o desenvolvimento de CSS utilizando recursos como variáveis, aninhamento, módulos, mesclas e herança. Embora o isolamento CSS não dê suporte nativo a pré-processadores CSS, como o Sass ou menos, a integração de pré-processadores CSS é direta, desde que a compilação do pré-processador ocorra antes de Blazor regravar os seletores CSS durante o processo de compilação. Usando o Visual Studio, por exemplo, configure a compilação de pré-processador existente como uma tarefa de **compilação anterior** no Gerenciador de executores de tarefas do Visual Studio.

Muitos pacotes NuGet de terceiros, como o [delegate. SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), podem COMPILAR arquivos Sass/SCSS no início do processo de compilação antes que o isolamento de CSS ocorra, e nenhuma configuração adicional adicional é necessária.

## <a name="css-isolation-configuration"></a>Configuração de isolamento de CSS

O isolamento de CSS é projetado para funcionar de forma integrada, mas fornece configuração para alguns cenários avançados, como quando há dependências em ferramentas ou fluxos de trabalho existentes.

### <a name="customize-scope-identifier-format"></a>Personalizar o formato do identificador de escopo

Por padrão, os identificadores de escopo usam o formato `b-<10-character-string>` . Para personalizar o formato do identificador de escopo, atualize o arquivo de projeto para um padrão desejado:

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

No exemplo anterior, o CSS gerado para `MyComponent.Razor.css` altera seu identificador de escopo de `b-<10-character-string>` para `my-custom-scope-identifier` .

### <a name="change-base-path-for-static-web-assets"></a>Alterar o caminho base para ativos da Web estáticos

O `scoped.styles.css` arquivo é gerado na raiz do aplicativo. No arquivo de projeto, use a `StaticWebAssetBasePath` propriedade para alterar o caminho padrão. O exemplo a seguir coloca o `scoped.styles.css` arquivo e o restante dos ativos do aplicativo, no `_content` caminho:

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a>Desabilitar o agrupamento automático

Para recusar a forma como Blazor o publica e carrega arquivos com escopo em tempo de execução, use a `DisableScopedCssBundling` propriedade. Ao usar essa propriedade, isso significa que outras ferramentas ou processos são responsáveis por pegar os arquivos CSS isolados do `obj` diretório e publicá-los e carregá-los em tempo de execução:

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```
