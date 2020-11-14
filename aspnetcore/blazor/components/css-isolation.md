---
title: BlazorIsolamento ASP.NET Core CSS
author: daveabrock
description: Saiba como o isolamento CSS permite que você alcance o CSS para seus componentes, o que pode simplificar seu CSS e evitar colisões com outros componentes ou bibliotecas.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/components/css-isolation
ms.openlocfilehash: 4fec0fa750b9209849030d0d6b7de8f4e163d62f
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570127"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a><span data-ttu-id="0ac1d-103">BlazorIsolamento ASP.NET Core CSS</span><span class="sxs-lookup"><span data-stu-id="0ac1d-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="0ac1d-104">Por [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="0ac1d-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="0ac1d-105">O isolamento CSS simplifica a superfície CSS de um aplicativo, impedindo dependências em estilos globais e ajuda a evitar conflitos de estilo entre componentes e bibliotecas.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="0ac1d-106">Habilitar isolamento de CSS</span><span class="sxs-lookup"><span data-stu-id="0ac1d-106">Enable CSS isolation</span></span> 

<span data-ttu-id="0ac1d-107">Para definir estilos específicos do componente, crie um `.razor.css` arquivo correspondente ao nome do `.razor` arquivo para o componente.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component.</span></span> <span data-ttu-id="0ac1d-108">Este `.razor.css` arquivo é um *arquivo CSS com escopo definido*.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-108">This `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="0ac1d-109">Para um `MyComponent` componente que tenha um `MyComponent.razor` arquivo, crie um arquivo junto com o componente chamado `MyComponent.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="0ac1d-109">For a `MyComponent` component that has a `MyComponent.razor` file, create a file alongside the component called `MyComponent.razor.css`.</span></span> <span data-ttu-id="0ac1d-110">O `MyComponent` valor no `.razor.css` nome do arquivo **não** diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-110">The `MyComponent` value in the `.razor.css` filename is **not** case-sensitive.</span></span>

<span data-ttu-id="0ac1d-111">Por exemplo, para adicionar o isolamento de CSS ao `Counter` componente no Blazor modelo de projeto padrão, adicione um novo arquivo chamado `Counter.razor.css` junto com o `Counter.razor` arquivo e, em seguida, adicione o seguinte CSS:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-111">For example to add CSS isolation to the `Counter` component in the default Blazor project template, add a new file named `Counter.razor.css` alongside the `Counter.razor` file, then add the following CSS:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="0ac1d-112">Os estilos definidos em `Counter.razor.css` são aplicados somente à saída renderizada do `Counter` componente.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-112">The styles defined in `Counter.razor.css` are only applied to the rendered output of the `Counter` component.</span></span> <span data-ttu-id="0ac1d-113">Quaisquer `h1` declarações CSS definidas em outro lugar no aplicativo não entram em conflito com os `Counter` estilos.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-113">Any `h1` CSS declarations defined elsewhere in the app don't conflict with `Counter` styles.</span></span>

> [!NOTE]
> <span data-ttu-id="0ac1d-114">Para garantir o isolamento de estilo quando o agrupamento ocorre, `@import` Razor os blocos não têm suporte com arquivos CSS com escopo definido.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-114">In order to guarantee style isolation when bundling occurs, `@import` Razor blocks aren't supported with scoped CSS files.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="0ac1d-115">Agrupamento de isolamento de CSS</span><span class="sxs-lookup"><span data-stu-id="0ac1d-115">CSS isolation bundling</span></span>

<span data-ttu-id="0ac1d-116">O isolamento de CSS ocorre no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-116">CSS isolation occurs at build time.</span></span> <span data-ttu-id="0ac1d-117">Durante esse processo, o Blazor reescreve seletores CSS para corresponder à marcação renderizada pelo componente.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-117">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="0ac1d-118">Esses estilos CSS reescritos são agrupados e produzidos como um ativo estático em `{PROJECT NAME}.styles.css` , em que o espaço reservado `{PROJECT NAME}` é o nome do produto ou do pacote referenciado.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-118">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="0ac1d-119">Por padrão, esses arquivos estáticos são referenciados no caminho raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-119">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="0ac1d-120">No aplicativo, referencie o arquivo agrupado inspecionando a referência dentro da `<head>` marca do HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-120">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="0ac1d-121">No arquivo empacotado, cada componente é associado a um identificador de escopo.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-121">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="0ac1d-122">Para cada componente com estilo, um atributo HTML é anexado com o formato `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="0ac1d-122">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="0ac1d-123">O identificador é exclusivo e diferente para cada aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-123">The identifier is unique and different for each app.</span></span> <span data-ttu-id="0ac1d-124">No componente renderizado `Counter` , Blazor acrescenta um identificador de escopo ao `h1` elemento:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-124">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="0ac1d-125">O `MyProjectName.styles.css` arquivo usa o identificador de escopo para agrupar uma declaração de estilo com seu componente.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-125">The `MyProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="0ac1d-126">O exemplo a seguir fornece o estilo para o `<h1>` elemento anterior:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-126">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="0ac1d-127">No momento da compilação, um pacote de projeto é criado com a Convenção `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` , em que o espaço reservado `{STATIC WEB ASSETS BASE PATH}` é o caminho base dos ativos da Web estáticos.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-127">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="0ac1d-128">Se outros projetos forem utilizados, como pacotes NuGet ou bibliotecas de [ Razor classes](xref:blazor/components/class-libraries), o arquivo agrupado:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-128">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="0ac1d-129">Faz referência aos estilos usando importações CSS.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-129">References the styles using CSS imports.</span></span>
* <span data-ttu-id="0ac1d-130">Não é publicado como um ativo da Web estático do aplicativo que consome os estilos.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-130">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="0ac1d-131">Suporte a componentes filho</span><span class="sxs-lookup"><span data-stu-id="0ac1d-131">Child component support</span></span>

<span data-ttu-id="0ac1d-132">Por padrão, o isolamento de CSS só se aplica ao componente que você associa ao formato `{COMPONENT NAME}.razor.css` , em que o espaço reservado `{COMPONENT NAME}` é geralmente o nome do componente.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-132">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="0ac1d-133">Para aplicar as alterações a um componente filho, use o `::deep` combinador para quaisquer elementos descendentes no arquivo do componente pai `.razor.css` .</span><span class="sxs-lookup"><span data-stu-id="0ac1d-133">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="0ac1d-134">O `::deep` combinador seleciona elementos que são *descendentes* do identificador de escopo gerado de um elemento.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-134">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="0ac1d-135">O exemplo a seguir mostra um componente pai chamado `Parent` com um componente filho chamado `Child` .</span><span class="sxs-lookup"><span data-stu-id="0ac1d-135">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="0ac1d-136">`Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-136">`Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="0ac1d-137">`Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-137">`Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="0ac1d-138">Atualize a `h1` declaração em `Parent.razor.css` com o `::deep` combinador para significar que a `h1` declaração de estilo deve ser aplicada ao componente pai e a seus filhos:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-138">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="0ac1d-139">O `h1` estilo agora se aplica aos `Parent` `Child` componentes e sem a necessidade de criar um arquivo CSS com escopo separado para o componente filho.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-139">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

> [!NOTE]
> <span data-ttu-id="0ac1d-140">O `::deep` combinador funciona apenas com elementos descendentes.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-140">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="0ac1d-141">A seguinte estrutura HTML aplica os `h1` estilos aos componentes conforme esperado:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-141">The following HTML structure applies the `h1` styles to components as expected:</span></span>
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> <span data-ttu-id="0ac1d-142">Nesse cenário, ASP.NET Core aplica o identificador de escopo do componente pai ao `div` elemento, portanto, o navegador sabe herdar estilos do componente pai.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-142">In this scenario, ASP.NET Core applies the parent component's scope identifier to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>
>
> <span data-ttu-id="0ac1d-143">No entanto, a exclusão do `div` elemento remove a relação descendente e o estilo **não** é aplicado ao componente filho:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-143">However, excluding the `div` element removes the descendant relationship, and the style is **not** applied to the child component:</span></span>
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a><span data-ttu-id="0ac1d-144">Suporte ao pré-processador do CSS</span><span class="sxs-lookup"><span data-stu-id="0ac1d-144">CSS preprocessor support</span></span>

<span data-ttu-id="0ac1d-145">Os pré-processadores CSS são úteis para melhorar o desenvolvimento de CSS utilizando recursos como variáveis, aninhamento, módulos, mesclas e herança.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-145">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="0ac1d-146">Embora o isolamento CSS não dê suporte nativo a pré-processadores CSS, como o Sass ou menos, a integração de pré-processadores CSS é direta, desde que a compilação do pré-processador ocorra antes de Blazor regravar os seletores CSS durante o processo de compilação.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-146">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="0ac1d-147">Usando o Visual Studio, por exemplo, configure a compilação de pré-processador existente como uma tarefa de **compilação anterior** no Gerenciador de executores de tarefas do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-147">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="0ac1d-148">Muitos pacotes NuGet de terceiros, como o [delegate. SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), podem COMPILAR arquivos Sass/SCSS no início do processo de compilação antes que o isolamento de CSS ocorra, e nenhuma configuração adicional adicional é necessária.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-148">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="0ac1d-149">Configuração de isolamento de CSS</span><span class="sxs-lookup"><span data-stu-id="0ac1d-149">CSS isolation configuration</span></span>

<span data-ttu-id="0ac1d-150">O isolamento de CSS é projetado para funcionar de forma integrada, mas fornece configuração para alguns cenários avançados, como quando há dependências em ferramentas ou fluxos de trabalho existentes.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-150">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="0ac1d-151">Personalizar o formato do identificador de escopo</span><span class="sxs-lookup"><span data-stu-id="0ac1d-151">Customize scope identifier format</span></span>

<span data-ttu-id="0ac1d-152">Por padrão, os identificadores de escopo usam o formato `b-<10-character-string>` .</span><span class="sxs-lookup"><span data-stu-id="0ac1d-152">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="0ac1d-153">Para personalizar o formato do identificador de escopo, atualize o arquivo de projeto para um padrão desejado:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-153">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="0ac1d-154">No exemplo anterior, o CSS gerado para `MyComponent.Razor.css` altera seu identificador de escopo de `b-<10-character-string>` para `my-custom-scope-identifier` .</span><span class="sxs-lookup"><span data-stu-id="0ac1d-154">In the preceding example, the CSS generated for `MyComponent.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="0ac1d-155">Alterar o caminho base para ativos da Web estáticos</span><span class="sxs-lookup"><span data-stu-id="0ac1d-155">Change base path for static web assets</span></span>

<span data-ttu-id="0ac1d-156">O `scoped.styles.css` arquivo é gerado na raiz do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-156">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="0ac1d-157">No arquivo de projeto, use a `StaticWebAssetBasePath` propriedade para alterar o caminho padrão.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-157">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="0ac1d-158">O exemplo a seguir coloca o `scoped.styles.css` arquivo e o restante dos ativos do aplicativo, no `_content` caminho:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-158">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="0ac1d-159">Desabilitar o agrupamento automático</span><span class="sxs-lookup"><span data-stu-id="0ac1d-159">Disable automatic bundling</span></span>

<span data-ttu-id="0ac1d-160">Para recusar a forma como Blazor o publica e carrega arquivos com escopo em tempo de execução, use a `DisableScopedCssBundling` propriedade.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-160">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="0ac1d-161">Ao usar essa propriedade, isso significa que outras ferramentas ou processos são responsáveis por pegar os arquivos CSS isolados do `obj` diretório e publicá-los e carregá-los em tempo de execução:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-161">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="no-locrazor-class-library-rcl-support"></a><span data-ttu-id="0ac1d-162">Razor suporte à biblioteca de classes (RCL)</span><span class="sxs-lookup"><span data-stu-id="0ac1d-162">Razor class library (RCL) support</span></span>

<span data-ttu-id="0ac1d-163">Quando uma [ Razor biblioteca de classes (RCL)](xref:razor-pages/ui-class) fornece estilos isolados, o `<link>` atributo da marca `href` aponta para `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css` , onde os espaços reservados são:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-163">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="0ac1d-164">`{STATIC WEB ASSET BASE PATH}`: O caminho base do ativo da Web estático.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-164">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="0ac1d-165">`{ASSEMBLY NAME}`: O nome do assembly da biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-165">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="0ac1d-166">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-166">In the following example:</span></span>

* <span data-ttu-id="0ac1d-167">O caminho base do ativo da Web estático é `_content/ClassLib` .</span><span class="sxs-lookup"><span data-stu-id="0ac1d-167">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="0ac1d-168">O nome do assembly da biblioteca de classes é `ClassLib` .</span><span class="sxs-lookup"><span data-stu-id="0ac1d-168">The class library's assembly name is `ClassLib`.</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="0ac1d-169">Para obter mais informações sobre RCLs e bibliotecas de componentes, consulte:</span><span class="sxs-lookup"><span data-stu-id="0ac1d-169">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="0ac1d-170"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="0ac1d-170"><xref:blazor/components/class-libraries>.</span></span>
