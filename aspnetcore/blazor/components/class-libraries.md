---
title: RazorBibliotecas de classes de componentes ASP.NET Core
author: guardrex
description: Descubra como os componentes podem ser incluídos em Blazor aplicativos de uma biblioteca de componentes externos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
uid: blazor/components/class-libraries
ms.openlocfilehash: afd1bfffae11520a5d9abccc1d2ee4cf3a46a4bf
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722456"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="892e3-103">RazorBibliotecas de classes de componentes ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="892e3-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="892e3-104">Por [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="892e3-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="892e3-105">Os componentes podem ser compartilhados em uma [ Razor biblioteca de classes (RCL)](xref:razor-pages/ui-class) em projetos.</span><span class="sxs-lookup"><span data-stu-id="892e3-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="892e3-106">Uma \* Razor biblioteca de classes de componentes\* pode ser incluída em:</span><span class="sxs-lookup"><span data-stu-id="892e3-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="892e3-107">Outro projeto na solução.</span><span class="sxs-lookup"><span data-stu-id="892e3-107">Another project in the solution.</span></span>
* <span data-ttu-id="892e3-108">Um pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="892e3-108">A NuGet package.</span></span>
* <span data-ttu-id="892e3-109">Uma biblioteca .NET referenciada.</span><span class="sxs-lookup"><span data-stu-id="892e3-109">A referenced .NET library.</span></span>

<span data-ttu-id="892e3-110">Assim como os componentes são tipos .NET regulares, os componentes fornecidos por um RCL são assemblies normais do .NET.</span><span class="sxs-lookup"><span data-stu-id="892e3-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="892e3-111">Criar um RCL</span><span class="sxs-lookup"><span data-stu-id="892e3-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="892e3-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="892e3-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="892e3-113">Criar um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="892e3-113">Create a new project.</span></span>
1. <span data-ttu-id="892e3-114">Selecione \*\* Razor biblioteca de classes\*\*.</span><span class="sxs-lookup"><span data-stu-id="892e3-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="892e3-115">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="892e3-115">Select **Next**.</span></span>
1. <span data-ttu-id="892e3-116">Na caixa de diálogo **criar uma nova Razor biblioteca de classes** , selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="892e3-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="892e3-117">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="892e3-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="892e3-118">Os exemplos neste tópico usam o nome do projeto `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="892e3-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="892e3-119">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="892e3-119">Select **Create**.</span></span>
1. <span data-ttu-id="892e3-120">Adicione o RCL a uma solução:</span><span class="sxs-lookup"><span data-stu-id="892e3-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="892e3-121">Clique com o botão direito do mouse na solução.</span><span class="sxs-lookup"><span data-stu-id="892e3-121">Right-click the solution.</span></span> <span data-ttu-id="892e3-122">Selecione **Adicionar**  >  **projeto existente**.</span><span class="sxs-lookup"><span data-stu-id="892e3-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="892e3-123">Navegue até o arquivo de projeto do RCL.</span><span class="sxs-lookup"><span data-stu-id="892e3-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="892e3-124">Selecione o arquivo de projeto do RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="892e3-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="892e3-125">Adicione uma referência ao RCL do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="892e3-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="892e3-126">Clique com o botão direito do mouse no projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="892e3-126">Right-click the app project.</span></span> <span data-ttu-id="892e3-127">Selecione **Adicionar**  >  **referência**.</span><span class="sxs-lookup"><span data-stu-id="892e3-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="892e3-128">Selecione o projeto RCL.</span><span class="sxs-lookup"><span data-stu-id="892e3-128">Select the RCL project.</span></span> <span data-ttu-id="892e3-129">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="892e3-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="892e3-130">Se a caixa de seleção **páginas de suporte e exibições** estiver marcada ao gerar o RCL do modelo, adicione também um `_Imports.razor` arquivo à raiz do projeto gerado com o seguinte conteúdo para habilitar a Razor criação de componentes:</span><span class="sxs-lookup"><span data-stu-id="892e3-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="892e3-131">Adicione manualmente o arquivo a raiz do projeto gerado.</span><span class="sxs-lookup"><span data-stu-id="892e3-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="892e3-132">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="892e3-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="892e3-133">Use o modelo de \*\* Razor biblioteca de classes\*\* ( `razorclasslib` ) com o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="892e3-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="892e3-134">No exemplo a seguir, um RCL é criado com o nome `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="892e3-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="892e3-135">A pasta que contém `ComponentLibrary` é criada automaticamente quando o comando é executado:</span><span class="sxs-lookup"><span data-stu-id="892e3-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="892e3-136">Se a `-s|--support-pages-and-views` opção for usada ao gerar o RCL do modelo, adicione também um `_Imports.razor` arquivo à raiz do projeto gerado com o seguinte conteúdo para habilitar a criação de Razor componentes:</span><span class="sxs-lookup"><span data-stu-id="892e3-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="892e3-137">Adicione manualmente o arquivo a raiz do projeto gerado.</span><span class="sxs-lookup"><span data-stu-id="892e3-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="892e3-138">Para adicionar a biblioteca a um projeto existente, use o [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) comando em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="892e3-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="892e3-139">No exemplo a seguir, o RCL é adicionado ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="892e3-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="892e3-140">Execute o seguinte comando na pasta do projeto do aplicativo com o caminho para a biblioteca:</span><span class="sxs-lookup"><span data-stu-id="892e3-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="892e3-141">Consumir um componente de biblioteca</span><span class="sxs-lookup"><span data-stu-id="892e3-141">Consume a library component</span></span>

<span data-ttu-id="892e3-142">Para consumir os componentes definidos em uma biblioteca em outro projeto, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="892e3-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="892e3-143">Use o nome completo do tipo com o namespace.</span><span class="sxs-lookup"><span data-stu-id="892e3-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="892e3-144">Use Razor a [`@using`](xref:mvc/views/razor#using) diretiva do.</span><span class="sxs-lookup"><span data-stu-id="892e3-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="892e3-145">Componentes individuais podem ser adicionados por nome.</span><span class="sxs-lookup"><span data-stu-id="892e3-145">Individual components can be added by name.</span></span>

<span data-ttu-id="892e3-146">Nos exemplos a seguir, `ComponentLibrary` é uma biblioteca de componentes que contém o `Component1` componente ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="892e3-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="892e3-147">O `Component1` componente é um componente de exemplo adicionado automaticamente pelo modelo de projeto RCL quando a biblioteca é criada.</span><span class="sxs-lookup"><span data-stu-id="892e3-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="892e3-148">Referencie o `Component1` componente usando seu namespace:</span><span class="sxs-lookup"><span data-stu-id="892e3-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="892e3-149">Como alternativa, traga a biblioteca para o escopo com uma [`@using`](xref:mvc/views/razor#using) diretiva e use o componente sem seu namespace:</span><span class="sxs-lookup"><span data-stu-id="892e3-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="892e3-150">Opcionalmente, inclua a `@using ComponentLibrary` diretiva no arquivo de nível superior `_Import.razor` para disponibilizar os componentes da biblioteca para um projeto inteiro.</span><span class="sxs-lookup"><span data-stu-id="892e3-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="892e3-151">Adicione a diretiva a um `_Import.razor` arquivo em qualquer nível para aplicar o namespace a um único componente ou a um conjunto de componentes dentro de uma pasta.</span><span class="sxs-lookup"><span data-stu-id="892e3-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="892e3-152">Para fornecer `Component1` a `my-component` classe CSS do componente, vincule à folha de estilos da biblioteca usando o [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) da estrutura em `Component1.razor` :</span><span class="sxs-lookup"><span data-stu-id="892e3-152">To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:</span></span>

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

<span data-ttu-id="892e3-153">Para fornecer a folha de estilos ao aplicativo, você pode vincular, como alternativa, a folha de estilos da biblioteca no `wwwroot/index.html` arquivo ( Blazor WebAssembly ) ou arquivo do aplicativo `Pages/_Host.cshtml` () Blazor Server :</span><span class="sxs-lookup"><span data-stu-id="892e3-153">To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<span data-ttu-id="892e3-154">Quando o `Link` componente é usado em um componente filho, o ativo vinculado também está disponível para qualquer outro componente filho do componente pai, desde que o filho com o `Link` componente seja renderizado.</span><span class="sxs-lookup"><span data-stu-id="892e3-154">When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered.</span></span> <span data-ttu-id="892e3-155">A distinção entre usar o `Link` componente em um componente filho e colocar uma `<link>` marca HTML no `wwwroot/index.html` ou `Pages/_Host.cshtml` é que a marca HTML renderizada de um componente de estrutura:</span><span class="sxs-lookup"><span data-stu-id="892e3-155">The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="892e3-156">Pode ser modificado pelo estado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="892e3-156">Can be modified by application state.</span></span> <span data-ttu-id="892e3-157">Uma marca HTML embutida em código `<link>` não pode ser modificada pelo estado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="892e3-157">A hard-coded `<link>` HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="892e3-158">É removido do HTML `<head>` quando o componente pai não é mais renderizado.</span><span class="sxs-lookup"><span data-stu-id="892e3-158">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="892e3-159">Para fornecer `Component1` a `my-component` classe CSS, vincule à folha de estilos da biblioteca no arquivo do aplicativo `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` arquivo ( Blazor Server ):</span><span class="sxs-lookup"><span data-stu-id="892e3-159">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="892e3-160">Criar uma Razor biblioteca de classes de componentes com ativos estáticos</span><span class="sxs-lookup"><span data-stu-id="892e3-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="892e3-161">Um RCL pode incluir ativos estáticos.</span><span class="sxs-lookup"><span data-stu-id="892e3-161">An RCL can include static assets.</span></span> <span data-ttu-id="892e3-162">Os ativos estáticos estão disponíveis para qualquer aplicativo que consuma a biblioteca.</span><span class="sxs-lookup"><span data-stu-id="892e3-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="892e3-163">Para obter mais informações, consulte <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="892e3-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="892e3-164">Fornecer componentes e ativos estáticos para vários Blazor aplicativos hospedados</span><span class="sxs-lookup"><span data-stu-id="892e3-164">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="892e3-165">Para obter mais informações, consulte <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="892e3-165">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a><span data-ttu-id="892e3-166">Analisador de compatibilidade de navegador para Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="892e3-166">Browser compatibility analyzer for Blazor WebAssembly</span></span>

<span data-ttu-id="892e3-167">Blazor WebAssembly os aplicativos direcionam a área completa da superfície do .NET API, mas nem todas as APIs do .NET têm suporte no Webassembly devido a restrições de área restrita do navegador.</span><span class="sxs-lookup"><span data-stu-id="892e3-167">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="892e3-168">APIs sem suporte são lançadas <xref:System.PlatformNotSupportedException> quando em execução no Webassembly.</span><span class="sxs-lookup"><span data-stu-id="892e3-168">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="892e3-169">Um analisador de compatibilidade de plataforma avisa o desenvolvedor quando o aplicativo usa APIs que não são compatíveis com as plataformas de destino do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="892e3-169">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="892e3-170">Para Blazor WebAssembly aplicativos, isso significa verificar se há suporte para APIs em navegadores.</span><span class="sxs-lookup"><span data-stu-id="892e3-170">For Blazor WebAssembly apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id="892e3-171">Anotar as APIs do .NET Framework para o analisador de compatibilidade é um processo contínuo; portanto, nem todas as APIs do .NET Framework estão anotadas no momento.</span><span class="sxs-lookup"><span data-stu-id="892e3-171">Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id="892e3-172">Blazor WebAssembly e Razor os projetos de biblioteca de classes habilitam *automaticamente* as verificações de compatibilidade do navegador adicionando `browser` como uma plataforma com suporte com o `SupportedPlatform` item do MSBuild.</span><span class="sxs-lookup"><span data-stu-id="892e3-172">Blazor WebAssembly and Razor class library projects *automatically* enable browser compatibilty checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id="892e3-173">Os desenvolvedores de biblioteca podem adicionar manualmente o `SupportedPlatform` item ao arquivo de projeto de uma biblioteca para habilitar o recurso:</span><span class="sxs-lookup"><span data-stu-id="892e3-173">Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

<span data-ttu-id="892e3-174">Ao criar uma biblioteca, indique que uma API específica não tem suporte em navegadores especificando `browser` para <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> :</span><span class="sxs-lookup"><span data-stu-id="892e3-174">When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="892e3-175">Para obter mais informações, consulte [anotando APIs como sem suporte em plataformas específicas (o repositório do GitHub dotnet/designs](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span><span class="sxs-lookup"><span data-stu-id="892e3-175">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="892e3-176">Blazor Isolamento de JavaScript e referências de objeto</span><span class="sxs-lookup"><span data-stu-id="892e3-176">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="892e3-177">Blazor habilita o isolamento de JavaScript em [módulos JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)padrão.</span><span class="sxs-lookup"><span data-stu-id="892e3-177">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="892e3-178">O isolamento de JavaScript oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="892e3-178">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="892e3-179">O JavaScript importado não polui mais o namespace global.</span><span class="sxs-lookup"><span data-stu-id="892e3-179">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="892e3-180">Os consumidores da biblioteca e dos componentes não são necessários para importar manualmente o JavaScript relacionado.</span><span class="sxs-lookup"><span data-stu-id="892e3-180">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="892e3-181">Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="892e3-181">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="892e3-182">Compilar, empacotar e enviar para o NuGet</span><span class="sxs-lookup"><span data-stu-id="892e3-182">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="892e3-183">Como as bibliotecas de componentes são bibliotecas padrão do .NET, o empacotamento e o envio deles para o NuGet não é diferente do empacotamento e do envio de nenhuma biblioteca para o NuGet.</span><span class="sxs-lookup"><span data-stu-id="892e3-183">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="892e3-184">O empacotamento é executado usando o [`dotnet pack`](/dotnet/core/tools/dotnet-pack) comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="892e3-184">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="892e3-185">Carregue o pacote no NuGet usando o [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) comando em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="892e3-185">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="892e3-186">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="892e3-186">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="892e3-187">Adicionar um arquivo de configuração de corte de IL (linguagem intermediária) XML a uma biblioteca</span><span class="sxs-lookup"><span data-stu-id="892e3-187">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="892e3-188">Adicionar um arquivo de configuração do vinculador de IL (linguagem intermediária) de XML a uma biblioteca</span><span class="sxs-lookup"><span data-stu-id="892e3-188">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
