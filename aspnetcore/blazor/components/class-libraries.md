---
title: RazorBibliotecas de classes de componentes ASP.NET Core
author: guardrex
description: Descubra como os componentes podem ser incluídos em Blazor aplicativos de uma biblioteca de componentes externos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 24a5b93a18cfe36c50d9739ba56d12aca41615c0
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570153"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a>RazorBibliotecas de classes de componentes ASP.NET Core

Por [Simon Timms](https://github.com/stimms)

Os componentes podem ser compartilhados em uma [ Razor biblioteca de classes (RCL)](xref:razor-pages/ui-class) em projetos. Uma *Razor biblioteca de classes de componentes* pode ser incluída em:

* Outro projeto na solução.
* Um pacote NuGet.
* Uma biblioteca .NET referenciada.

Assim como os componentes são tipos .NET regulares, os componentes fornecidos por um RCL são assemblies normais do .NET.

## <a name="create-an-rcl"></a>Criar um RCL

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Criar um novo projeto.
1. Selecione **Razor biblioteca de classes**. Selecione **Avançar**.
1. Na caixa de diálogo **criar uma nova Razor biblioteca de classes** , selecione **criar**.
1. Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão. Os exemplos neste tópico usam o nome do projeto `ComponentLibrary` . Selecione **Criar**.
1. Adicione o RCL a uma solução:
   1. Clique com o botão direito do mouse na solução. Selecione **Adicionar**  >  **projeto existente**.
   1. Navegue até o arquivo de projeto do RCL.
   1. Selecione o arquivo de projeto do RCL ( `.csproj` ).
1. Adicione uma referência ao RCL do aplicativo:
   1. Clique com o botão direito do mouse no projeto do aplicativo. Selecione **Adicionar**  >  **referência**.
   1. Selecione o projeto RCL. Selecione **OK**.

> [!NOTE]
> Se a caixa de seleção **páginas de suporte e exibições** estiver marcada ao gerar o RCL do modelo, adicione também um `_Imports.razor` arquivo à raiz do projeto gerado com o seguinte conteúdo para habilitar a Razor criação de componentes:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Adicione manualmente o arquivo a raiz do projeto gerado.

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

1. Use o modelo de **Razor biblioteca de classes** ( `razorclasslib` ) com o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando em um shell de comando. No exemplo a seguir, um RCL é criado com o nome `ComponentLibrary` . A pasta que contém `ComponentLibrary` é criada automaticamente quando o comando é executado:

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > Se a `-s|--support-pages-and-views` opção for usada ao gerar o RCL do modelo, adicione também um `_Imports.razor` arquivo à raiz do projeto gerado com o seguinte conteúdo para habilitar a criação de Razor componentes:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Adicione manualmente o arquivo a raiz do projeto gerado.

1. Para adicionar a biblioteca a um projeto existente, use o [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) comando em um shell de comando. No exemplo a seguir, o RCL é adicionado ao aplicativo. Execute o seguinte comando na pasta do projeto do aplicativo com o caminho para a biblioteca:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Consumir um componente de biblioteca

Para consumir os componentes definidos em uma biblioteca em outro projeto, use uma das seguintes abordagens:

* Use o nome completo do tipo com o namespace.
* Use Razor a [`@using`](xref:mvc/views/razor#using) diretiva do. Componentes individuais podem ser adicionados por nome.

Nos exemplos a seguir, `ComponentLibrary` é uma biblioteca de componentes que contém o `Component1` componente ( `Component1.razor` ). O `Component1` componente é um componente de exemplo adicionado automaticamente pelo modelo de projeto RCL quando a biblioteca é criada.

Referencie o `Component1` componente usando seu namespace:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

Como alternativa, traga a biblioteca para o escopo com uma [`@using`](xref:mvc/views/razor#using) diretiva e use o componente sem seu namespace:

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

Opcionalmente, inclua a `@using ComponentLibrary` diretiva no arquivo de nível superior `_Import.razor` para disponibilizar os componentes da biblioteca para um projeto inteiro. Adicione a diretiva a um `_Import.razor` arquivo em qualquer nível para aplicar o namespace a um único componente ou a um conjunto de componentes dentro de uma pasta.

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

Para fornecer `Component1` a `my-component` classe CSS, vincule à folha de estilos da biblioteca no arquivo do aplicativo `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` arquivo ( Blazor Server ):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a>Criar uma Razor biblioteca de classes de componentes com ativos estáticos

Um RCL pode incluir ativos estáticos. Os ativos estáticos estão disponíveis para qualquer aplicativo que consuma a biblioteca. Para obter mais informações, consulte <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a>Fornecer componentes e ativos estáticos para vários Blazor aplicativos hospedados

Para obter mais informações, consulte <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a>Analisador de compatibilidade de navegador para Blazor WebAssembly

Blazor WebAssembly os aplicativos direcionam a área completa da superfície do .NET API, mas nem todas as APIs do .NET têm suporte no Webassembly devido a restrições de área restrita do navegador. APIs sem suporte são lançadas <xref:System.PlatformNotSupportedException> quando em execução no Webassembly. Um analisador de compatibilidade de plataforma avisa o desenvolvedor quando o aplicativo usa APIs que não são compatíveis com as plataformas de destino do aplicativo. Para Blazor WebAssembly aplicativos, isso significa verificar se há suporte para APIs em navegadores. Anotar as APIs do .NET Framework para o analisador de compatibilidade é um processo contínuo; portanto, nem todas as APIs do .NET Framework estão anotadas no momento.

Blazor WebAssembly e Razor os projetos de biblioteca de classes habilitam *automaticamente* as verificações de compatibilidade do navegador adicionando `browser` como uma plataforma com suporte com o `SupportedPlatform` item do MSBuild. Os desenvolvedores de biblioteca podem adicionar manualmente o `SupportedPlatform` item ao arquivo de projeto de uma biblioteca para habilitar o recurso:

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

Ao criar uma biblioteca, indique que uma API específica não tem suporte em navegadores especificando `browser` para <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute> :

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

Para obter mais informações, consulte [anotando APIs como sem suporte em plataformas específicas (o repositório do GitHub dotnet/designs](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor Isolamento de JavaScript e referências de objeto

Blazor habilita o isolamento de JavaScript em [módulos JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)padrão. O isolamento de JavaScript oferece os seguintes benefícios:

* O JavaScript importado não polui mais o namespace global.
* Os consumidores da biblioteca e dos componentes não são necessários para importar manualmente o JavaScript relacionado.

Para obter mais informações, consulte <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a>Compilar, empacotar e enviar para o NuGet

Como as bibliotecas de componentes são bibliotecas padrão do .NET, o empacotamento e o envio deles para o NuGet não é diferente do empacotamento e do envio de nenhuma biblioteca para o NuGet. O empacotamento é executado usando o [`dotnet pack`](/dotnet/core/tools/dotnet-pack) comando em um shell de comando:

```dotnetcli
dotnet pack
```

Carregue o pacote no NuGet usando o [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) comando em um shell de comando.

## <a name="additional-resources"></a>Recursos adicionais

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Adicionar um arquivo de configuração de corte de IL (linguagem intermediária) XML a uma biblioteca](xref:blazor/host-and-deploy/configure-trimmer)
* [Suporte de isolamento de CSS com Razor bibliotecas de classes](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Adicionar um arquivo de configuração do vinculador de IL (linguagem intermediária) de XML a uma biblioteca](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
