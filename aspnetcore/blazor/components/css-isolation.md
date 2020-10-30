---
title: :::no-loc(Blazor):::Isolamento ASP.NET Core CSS
author: daveabrock
description: Saiba como o isolamento CSS permite que você alcance o CSS para seus componentes, o que pode simplificar seu CSS e evitar colisões com outros componentes ou bibliotecas.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/components/css-isolation
ms.openlocfilehash: 628e7dc897912beaae0df792b82958517ac70ca4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056316"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a><span data-ttu-id="bc8bb-103">:::no-loc(Blazor):::Isolamento ASP.NET Core CSS</span><span class="sxs-lookup"><span data-stu-id="bc8bb-103">ASP.NET Core :::no-loc(Blazor)::: CSS isolation</span></span>

<span data-ttu-id="bc8bb-104">Por [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="bc8bb-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="bc8bb-105">O isolamento CSS simplifica a superfície CSS de um aplicativo, impedindo dependências em estilos globais e ajuda a evitar conflitos de estilo entre componentes e bibliotecas.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="bc8bb-106">Habilitar isolamento de CSS</span><span class="sxs-lookup"><span data-stu-id="bc8bb-106">Enable CSS isolation</span></span> 

<span data-ttu-id="bc8bb-107">Para definir estilos específicos do componente, crie um `razor.css` arquivo correspondente ao nome do `.razor` arquivo para o componente.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-107">To define component-specific styles, create a `razor.css` file matching the name of the `.razor` file for the component.</span></span> <span data-ttu-id="bc8bb-108">Este `razor.css` arquivo é um *arquivo CSS com escopo definido* .</span><span class="sxs-lookup"><span data-stu-id="bc8bb-108">This `razor.css` file is a *scoped CSS file* .</span></span> 

<span data-ttu-id="bc8bb-109">Para um `MyComponent` componente que tenha um `MyComponent.razor` arquivo, crie um arquivo junto com o componente chamado `MyComponent.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="bc8bb-109">For a `MyComponent` component that has a `MyComponent.razor` file, create a file alongside the component called `MyComponent.razor.css`.</span></span> <span data-ttu-id="bc8bb-110">O `MyComponent` valor no `razor.css` nome do arquivo **não** diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-110">The `MyComponent` value in the `razor.css` filename is **not** case-sensitive.</span></span>

<span data-ttu-id="bc8bb-111">Por exemplo, para adicionar o isolamento de CSS ao `Counter` componente no :::no-loc(Blazor)::: modelo de projeto padrão, adicione um novo arquivo chamado `Counter.razor.css` junto com o `Counter.razor` arquivo e, em seguida, adicione o seguinte CSS:</span><span class="sxs-lookup"><span data-stu-id="bc8bb-111">For example to add CSS isolation to the `Counter` component in the default :::no-loc(Blazor)::: project template, add a new file named `Counter.razor.css` alongside the `Counter.razor` file, then add the following CSS:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="bc8bb-112">Os estilos definidos em `Counter.razor.css` são aplicados somente à saída renderizada do `Counter` componente.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-112">The styles defined in `Counter.razor.css` are only applied to the rendered output of the `Counter` component.</span></span> <span data-ttu-id="bc8bb-113">Quaisquer `h1` declarações CSS definidas em outro lugar no aplicativo não entram em conflito com os `Counter` estilos.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-113">Any `h1` CSS declarations defined elsewhere in the app don't conflict with `Counter` styles.</span></span>

> [!NOTE]
> <span data-ttu-id="bc8bb-114">Para garantir o isolamento de estilo quando o agrupamento ocorre, `@import` :::no-loc(Razor)::: os blocos não têm suporte com arquivos CSS com escopo definido.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-114">In order to guarantee style isolation when bundling occurs, `@import` :::no-loc(Razor)::: blocks aren't supported with scoped CSS files.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="bc8bb-115">Agrupamento de isolamento de CSS</span><span class="sxs-lookup"><span data-stu-id="bc8bb-115">CSS isolation bundling</span></span>

<span data-ttu-id="bc8bb-116">O isolamento de CSS ocorre no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-116">CSS isolation occurs at build time.</span></span> <span data-ttu-id="bc8bb-117">Durante esse processo, o :::no-loc(Blazor)::: reescreve seletores CSS para corresponder à marcação renderizada pelo componente.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-117">During this process, :::no-loc(Blazor)::: rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="bc8bb-118">Esses estilos CSS reescritos são agrupados e produzidos como um ativo estático em `{PROJECT NAME}.styles.css` , em que o espaço reservado `{PROJECT NAME}` é o nome do produto ou do pacote referenciado.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-118">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="bc8bb-119">Por padrão, esses arquivos estáticos são referenciados no caminho raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-119">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="bc8bb-120">No aplicativo, referencie o arquivo agrupado inspecionando a referência dentro da `<head>` marca do HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="bc8bb-120">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="bc8bb-121">No arquivo empacotado, cada componente é associado a um identificador de escopo.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-121">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="bc8bb-122">Para cada componente com estilo, um atributo HTML é anexado com o formato `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="bc8bb-122">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="bc8bb-123">O identificador é exclusivo e diferente para cada aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-123">The identifier is unique and different for each app.</span></span> <span data-ttu-id="bc8bb-124">No componente renderizado `Counter` , :::no-loc(Blazor)::: acrescenta um identificador de escopo ao `h1` elemento:</span><span class="sxs-lookup"><span data-stu-id="bc8bb-124">In the rendered `Counter` component, :::no-loc(Blazor)::: appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="bc8bb-125">O `MyProjectName.styles.css` arquivo usa o identificador de escopo para agrupar uma declaração de estilo com seu componente.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-125">The `MyProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="bc8bb-126">O exemplo a seguir fornece o estilo para o `<h1>` elemento anterior:</span><span class="sxs-lookup"><span data-stu-id="bc8bb-126">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="bc8bb-127">No momento da compilação, um pacote de projeto é criado com a Convenção `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` , em que o espaço reservado `{STATIC WEB ASSETS BASE PATH}` é o caminho base dos ativos da Web estáticos.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-127">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="bc8bb-128">Se outros projetos forem utilizados, como pacotes NuGet ou bibliotecas de [ :::no-loc(Razor)::: classes](xref:blazor/components/class-libraries), o arquivo agrupado:</span><span class="sxs-lookup"><span data-stu-id="bc8bb-128">If other projects are utilized, such as NuGet packages or [:::no-loc(Razor)::: class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="bc8bb-129">Faz referência aos estilos usando importações CSS.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-129">References the styles using CSS imports.</span></span>
* <span data-ttu-id="bc8bb-130">Não é publicado como um ativo da Web estático do aplicativo que consome os estilos.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-130">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="bc8bb-131">Suporte a componentes filho</span><span class="sxs-lookup"><span data-stu-id="bc8bb-131">Child component support</span></span>

<span data-ttu-id="bc8bb-132">Por padrão, o isolamento de CSS só se aplica ao componente que você associa ao formato `{COMPONENT NAME}.razor.css` , em que o espaço reservado `{COMPONENT NAME}` é geralmente o nome do componente.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-132">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="bc8bb-133">Para aplicar as alterações a um componente filho, use o `::deep` combinador para quaisquer elementos descendentes no arquivo do componente pai `razor.css` .</span><span class="sxs-lookup"><span data-stu-id="bc8bb-133">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `razor.css` file.</span></span> <span data-ttu-id="bc8bb-134">O `::deep` combinador seleciona elementos que são *descendentes* do identificador de escopo gerado de um elemento.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-134">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="bc8bb-135">O exemplo a seguir mostra um componente pai chamado `Parent` com um componente filho chamado `Child` .</span><span class="sxs-lookup"><span data-stu-id="bc8bb-135">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="bc8bb-136">`Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="bc8bb-136">`Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="bc8bb-137">`Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="bc8bb-137">`Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="bc8bb-138">Atualize a `h1` declaração em `Parent.razor.css` com o `::deep` combinador para significar que a `h1` declaração de estilo deve ser aplicada ao componente pai e a seus filhos:</span><span class="sxs-lookup"><span data-stu-id="bc8bb-138">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="bc8bb-139">O `h1` estilo agora se aplica aos `Parent` `Child` componentes e sem a necessidade de criar um arquivo CSS com escopo separado para o componente filho.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-139">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

> [!NOTE]
> <span data-ttu-id="bc8bb-140">O `::deep` combinador funciona apenas com elementos descendentes.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-140">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="bc8bb-141">A seguinte estrutura HTML aplica os `h1` estilos aos componentes conforme esperado:</span><span class="sxs-lookup"><span data-stu-id="bc8bb-141">The following HTML structure applies the `h1` styles to components as expected:</span></span>
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> <span data-ttu-id="bc8bb-142">Nesse cenário, ASP.NET Core aplica o identificador de escopo do componente pai ao `div` elemento, portanto, o navegador sabe herdar estilos do componente pai.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-142">In this scenario, ASP.NET Core applies the parent component's scope identifier to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>
>
> <span data-ttu-id="bc8bb-143">No entanto, a exclusão do `div` elemento remove a relação descendente e o estilo **não** é aplicado ao componente filho:</span><span class="sxs-lookup"><span data-stu-id="bc8bb-143">However, excluding the `div` element removes the descendant relationship, and the style is **not** applied to the child component:</span></span>
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a><span data-ttu-id="bc8bb-144">Suporte ao pré-processador do CSS</span><span class="sxs-lookup"><span data-stu-id="bc8bb-144">CSS preprocessor support</span></span>

<span data-ttu-id="bc8bb-145">Os pré-processadores CSS são úteis para melhorar o desenvolvimento de CSS utilizando recursos como variáveis, aninhamento, módulos, mesclas e herança.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-145">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="bc8bb-146">Embora o isolamento CSS não dê suporte nativo a pré-processadores CSS, como o Sass ou menos, a integração de pré-processadores CSS é direta, desde que a compilação do pré-processador ocorra antes de :::no-loc(Blazor)::: regravar os seletores CSS durante o processo de compilação.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-146">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before :::no-loc(Blazor)::: rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="bc8bb-147">Usando o Visual Studio, por exemplo, configure a compilação de pré-processador existente como uma tarefa de **compilação anterior** no Gerenciador de executores de tarefas do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-147">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="bc8bb-148">Muitos pacotes NuGet de terceiros, como o [delegate. SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), podem COMPILAR arquivos Sass/SCSS no início do processo de compilação antes que o isolamento de CSS ocorra, e nenhuma configuração adicional adicional é necessária.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-148">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="bc8bb-149">Configuração de isolamento de CSS</span><span class="sxs-lookup"><span data-stu-id="bc8bb-149">CSS isolation configuration</span></span>

<span data-ttu-id="bc8bb-150">O isolamento de CSS é projetado para funcionar de forma integrada, mas fornece configuração para alguns cenários avançados, como quando há dependências em ferramentas ou fluxos de trabalho existentes.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-150">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="bc8bb-151">Personalizar o formato do identificador de escopo</span><span class="sxs-lookup"><span data-stu-id="bc8bb-151">Customize scope identifier format</span></span>

<span data-ttu-id="bc8bb-152">Por padrão, os identificadores de escopo usam o formato `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="bc8bb-152">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="bc8bb-153">Para personalizar o formato do identificador de escopo, atualize o arquivo de projeto para um padrão desejado:</span><span class="sxs-lookup"><span data-stu-id="bc8bb-153">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="bc8bb-154">No exemplo anterior, o CSS gerado para `MyComponent.:::no-loc(Razor):::.css` altera seu identificador de escopo de `b-<10-character-string>` para `my-custom-scope-identifier` .</span><span class="sxs-lookup"><span data-stu-id="bc8bb-154">In the preceding example, the CSS generated for `MyComponent.:::no-loc(Razor):::.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="bc8bb-155">Alterar o caminho base para ativos da Web estáticos</span><span class="sxs-lookup"><span data-stu-id="bc8bb-155">Change base path for static web assets</span></span>

<span data-ttu-id="bc8bb-156">O `scoped.styles.css` arquivo é gerado na raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-156">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="bc8bb-157">No arquivo de projeto, use a `StaticWebAssetBasePath` propriedade para alterar o caminho padrão.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-157">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="bc8bb-158">O exemplo a seguir coloca o `scoped.styles.css` arquivo e o restante dos ativos do aplicativo, no `_content` caminho:</span><span class="sxs-lookup"><span data-stu-id="bc8bb-158">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="bc8bb-159">Desabilitar o agrupamento automático</span><span class="sxs-lookup"><span data-stu-id="bc8bb-159">Disable automatic bundling</span></span>

<span data-ttu-id="bc8bb-160">Para recusar a forma como :::no-loc(Blazor)::: o publica e carrega arquivos com escopo em tempo de execução, use a `DisableScopedCssBundling` propriedade.</span><span class="sxs-lookup"><span data-stu-id="bc8bb-160">To opt out of how :::no-loc(Blazor)::: publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="bc8bb-161">Ao usar essa propriedade, isso significa que outras ferramentas ou processos são responsáveis por pegar os arquivos CSS isolados do `obj` diretório e publicá-los e carregá-los em tempo de execução:</span><span class="sxs-lookup"><span data-stu-id="bc8bb-161">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```
