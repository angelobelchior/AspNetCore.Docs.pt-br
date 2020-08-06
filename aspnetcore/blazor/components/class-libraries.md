---
title: RazorBibliotecas de classes de componentes ASP.NET Core
author: guardrex
description: Descubra como os componentes podem ser incluídos em Blazor aplicativos de uma biblioteca de componentes externos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: 8293d61f88f53e55d94b114ca2143fdfb6fd8468
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819061"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a>RazorBibliotecas de classes de componentes ASP.NET Core

Por [Simon Timms](https://github.com/stimms)

Os componentes podem ser compartilhados em uma [ Razor biblioteca de classes (RCL)](xref:razor-pages/ui-class) em projetos. Uma * Razor biblioteca de classes de componentes* pode ser incluída em:

* Outro projeto na solução.
* Um pacote NuGet.
* Uma biblioteca .NET referenciada.

Assim como os componentes são tipos .NET regulares, os componentes fornecidos por um RCL são assemblies normais do .NET.

## <a name="create-an-rcl"></a>Criar um RCL

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Criar um novo projeto.
1. Selecione ** Razor biblioteca de classes**. Selecione **Avançar**.
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

1. Use o modelo de ** Razor biblioteca de classes** ( `razorclasslib` ) com o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando em um shell de comando. No exemplo a seguir, um RCL é criado com o nome `ComponentLibrary` . A pasta que contém `ComponentLibrary` é criada automaticamente quando o comando é executado:

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

::: moniker range=">= aspnetcore-5.0"

Para fornecer `Component1` a `my-component` classe CSS do componente, vincule à folha de estilos da biblioteca usando o [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) da estrutura em `Component1.razor` :

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

Para fornecer a folha de estilos ao aplicativo, você pode vincular, como alternativa, a folha de estilos da biblioteca no `wwwroot/index.html` arquivo ( Blazor WebAssembly ) ou arquivo do aplicativo `Pages/_Host.cshtml` () Blazor Server :

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

Quando o `Link` componente é usado em um componente filho, o ativo vinculado também está disponível para qualquer outro componente filho do componente pai, desde que o filho com o `Link` componente seja renderizado. A distinção entre usar o `Link` componente em um componente filho e colocar uma `<link>` marca HTML no `wwwroot/index.html` ou `Pages/_Host.cshtml` é que a marca HTML renderizada de um componente de estrutura:

* Pode ser modificado pelo estado do aplicativo. Uma marca HTML embutida em código `<link>` não pode ser modificada pelo estado do aplicativo.
* É removido do HTML `<head>` quando o componente pai não é mais renderizado.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Para fornecer `Component1` a `my-component` classe CSS, vincule à folha de estilos da biblioteca no arquivo do aplicativo `wwwroot/index.html` ( Blazor WebAssembly ) ou `Pages/_Host.cshtml` arquivo ( Blazor Server ):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a>Criar uma Razor biblioteca de classes de componentes com ativos estáticos

Um RCL pode incluir ativos estáticos. Os ativos estáticos estão disponíveis para qualquer aplicativo que consuma a biblioteca. Para obter mais informações, consulte <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a>Fornecer componentes e ativos estáticos para vários Blazor aplicativos hospedados

Para obter mais informações, consulte <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.

## <a name="build-pack-and-ship-to-nuget"></a>Compilar, empacotar e enviar para o NuGet

Como as bibliotecas de componentes são bibliotecas padrão do .NET, o empacotamento e o envio deles para o NuGet não é diferente do empacotamento e do envio de nenhuma biblioteca para o NuGet. O empacotamento é executado usando o [`dotnet pack`](/dotnet/core/tools/dotnet-pack) comando em um shell de comando:

```dotnetcli
dotnet pack
```

Carregue o pacote no NuGet usando o [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) comando em um shell de comando.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:razor-pages/ui-class>
* [Adicionar um arquivo de configuração do vinculador XML a uma biblioteca](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
