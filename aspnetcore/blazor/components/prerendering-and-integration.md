---
title: PreRender e integre componentes de ASP.NET Core Razor
author: guardrex
description: Saiba mais sobre Razor cenários de integração de componentes para Blazor aplicativos, incluindo a renderização de Razor componentes no servidor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: affca6c9b585b91787f94a13144d07bedfefdd37
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431698"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a>PreRender e integre componentes de ASP.NET Core Razor

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

Razor os componentes podem ser integrados em Razor páginas e aplicativos MVC em uma solução hospedada Blazor WebAssembly . Quando a página ou a exibição é renderizada, os componentes podem ser renderizados ao mesmo tempo.

## <a name="configuration"></a>Configuração

Para configurar o pré-processamento para um Blazor WebAssembly aplicativo:

1. Hospede o Blazor WebAssembly aplicativo em um aplicativo ASP.NET Core. Um Blazor WebAssembly aplicativo autônomo pode ser adicionado a uma solução de ASP.NET Core, ou você pode usar um Blazor WebAssembly aplicativo hospedado criado a partir do Blazor modelo de projeto hospedado.

1. Remova o arquivo estático padrão `wwwroot/index.html` do Blazor WebAssembly projeto do cliente.

1. Exclua a seguinte linha no `Program.Main` projeto do cliente:

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. Adicione um `Pages/_Host.cshtml` arquivo ao projeto do servidor. Você pode obter um `_Host.cshtml` arquivo de um aplicativo criado a partir do Blazor Server modelo com o `dotnet new blazorserver -o BlazorServer` comando em um shell de comando. Depois de colocar o `Pages/_Host.cshtml` arquivo no aplicativo de servidor da solução hospedada Blazor WebAssembly , faça as seguintes alterações no arquivo:

   * Defina o namespace para a pasta do aplicativo do servidor `Pages` (por exemplo, `@namespace BlazorHosted.Server.Pages` ).
   * Defina uma [`@using`](xref:mvc/views/razor#using) diretiva para o projeto cliente (por exemplo, `@using BlazorHosted.Client` ).
   * Atualize os links de folha de estilos para apontar para a folha de estilos do aplicativo Webassembly. No exemplo a seguir, o namespace do aplicativo cliente é `BlazorHosted.Client` :

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * Atualize o `render-mode` do [auxiliar de marca do componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para PreRender o `App` componente raiz:

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * Atualize a Blazor origem do script para usar o script do lado do cliente Blazor WebAssembly :

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. No `Startup.Configure` ( `Startup.cs` ) do projeto do servidor:

   * Chame `UseDeveloperExceptionPage` no app Builder no ambiente de desenvolvimento.
   * Chame `UseBlazorFrameworkFiles` no app Builder.
   * Altere o fallback da `index.html` página ( `endpoints.MapFallbackToFile("index.html");` ) para a `_Host.cshtml` página.

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a>Renderizar componentes em uma página ou exibição com o auxiliar de marca do componente

O auxiliar de marca de componente dá suporte a dois modos de renderização para renderizar um componente de um Blazor WebAssembly aplicativo em uma página ou exibição:

* `WebAssembly`: Renderiza um marcador para um Blazor WebAssembly aplicativo para usar para incluir um componente interativo quando carregado no navegador. O componente não é renderizado. Essa opção torna mais fácil renderizar diferentes Blazor WebAssembly componentes em páginas diferentes.
* `WebAssemblyPrerendered`: Coloca o componente em HTML estático e inclui um marcador para um Blazor WebAssembly aplicativo para uso posterior para tornar o componente interativo quando carregado no navegador.

No exemplo de Razor páginas a seguir, o `Counter` componente é renderizado em uma página. Para tornar o componente interativo, o Blazor WebAssembly script é incluído na [seção renderizar](xref:mvc/views/layout#sections)da página. Para evitar o uso do namespace completo para o `Counter` componente com o auxiliar de marca de componente ( `{APP ASSEMBLY}.Pages.Counter` ), adicione uma [`@using`](xref:mvc/views/razor#using) diretiva para o namespace do aplicativo cliente `Pages` . No exemplo a seguir, o namespace do aplicativo cliente é `BlazorHosted.Client` :

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura se o componente:

* É renderizado na página.
* É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.

Para obter mais informações sobre o auxiliar de marca de componente, incluindo a passagem de parâmetros e <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuração, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

O exemplo anterior requer que o layout do aplicativo do servidor ( `_Layout.cshtml` ) inclua uma [seção render](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A> ) para o script dentro da `</body>` marca de fechamento:

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

O `_Layout.cshtml` arquivo está localizado na `Pages/Shared` pasta em um Razor aplicativo ou pasta de páginas `Views/Shared` em um aplicativo MVC.

Se o aplicativo também deve estilizar componentes com os estilos no Blazor WebAssembly aplicativo, inclua os estilos do aplicativo no `_Layout.cshtml` arquivo. No exemplo a seguir, o namespace do aplicativo cliente é `BlazorHosted.Client` :

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a>Renderizar componentes em uma página ou exibição com um seletor de CSS

Adicione componentes raiz ao projeto do *cliente* no `Program.Main` ( `Program.cs` ). No exemplo a seguir, o `Counter` componente é declarado como um componente raiz com um seletor CSS que seleciona o elemento com o `id` que corresponde a `my-counter` . No exemplo a seguir, o namespace do aplicativo cliente é `BlazorHosted.Client` :

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

No exemplo de Razor páginas a seguir, o `Counter` componente é renderizado em uma página. Para tornar o componente interativo, o Blazor WebAssembly script é incluído na [seção renderizar](xref:mvc/views/layout#sections)da página:

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

O exemplo anterior requer que o layout do aplicativo do servidor ( `_Layout.cshtml` ) inclua uma [seção render](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A> ) para o script dentro da `</body>` marca de fechamento:

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

O `_Layout.cshtml` arquivo está localizado na `Pages/Shared` pasta em um Razor aplicativo ou pasta de páginas `Views/Shared` em um aplicativo MVC.

Se o aplicativo também deve estilizar componentes com os estilos no Blazor WebAssembly aplicativo, inclua os estilos do aplicativo no `_Layout.cshtml` arquivo. No exemplo a seguir, o namespace do aplicativo cliente é `BlazorHosted.Client` :

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

A integração de Razor componentes em Razor páginas e aplicativos MVC em uma solução hospedada Blazor WebAssembly tem suporte no ASP.NET Core no .NET 5 ou posterior. Selecione uma versão do .NET 5 ou posterior deste artigo.

::: moniker-end

::: zone-end

::: zone pivot="server"

Razor os componentes podem ser integrados em Razor páginas e aplicativos MVC em um Blazor Server aplicativo. Quando a página ou a exibição é renderizada, os componentes podem ser renderizados ao mesmo tempo.

Depois de [Configurar o aplicativo](#configuration), use as diretrizes nas seções a seguir, dependendo dos requisitos do aplicativo:

* Componentes roteáveis: para componentes que são roteáveis diretamente das solicitações do usuário. Siga estas diretrizes para que os visitantes possam fazer uma solicitação HTTP em seu navegador para um componente com uma [`@page`](xref:mvc/views/razor#page) diretiva.
  * [Usar componentes roteáveis em um Razor aplicativo de páginas](#use-routable-components-in-a-razor-pages-app)
  * [Usar componentes roteáveis em um aplicativo MVC](#use-routable-components-in-an-mvc-app)
* [Renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view): para componentes que não são roteáveis diretamente das solicitações do usuário. Siga estas diretrizes quando o aplicativo inserir componentes em páginas e exibições existentes com o [auxiliar de marca de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

## <a name="configuration"></a>Configuração

Uma Razor página existente ou um aplicativo MVC pode integrar Razor componentes em páginas e exibições:

1. No arquivo de layout do aplicativo ( `_Layout.cshtml` ):

   * Adicione a seguinte `<base>` marcação ao `<head>` elemento:

     ```html
     <base href="~/" />
     ```

     O `href` valor (o *caminho base do aplicativo* ) no exemplo anterior pressupõe que o aplicativo reside no caminho da URL raiz ( `/` ). Se o aplicativo for um subaplicativo, siga as orientações na seção *caminho base do aplicativo* do <xref:blazor/host-and-deploy/index#app-base-path> artigo.

     O `_Layout.cshtml` arquivo está localizado na `Pages/Shared` pasta em um Razor aplicativo ou pasta de páginas `Views/Shared` em um aplicativo MVC.

   * Adicione uma `<script>` marca para o `blazor.server.js` script imediatamente antes da `Scripts` seção render:

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     A estrutura adiciona o `blazor.server.js` script ao aplicativo. Não é necessário adicionar manualmente o script ao aplicativo.

1. Adicione um `_Imports.razor` arquivo à pasta raiz do projeto com o seguinte conteúdo (altere o último namespace, `MyAppNamespace` , para o namespace do aplicativo):

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. No `Startup.ConfigureServices` , registre o Blazor Server serviço:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. No `Startup.Configure` , adicione o Blazor ponto de extremidade do hub a `app.UseEndpoints` :

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integre componentes em qualquer página ou exibição. Para obter mais informações, consulte a seção [renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view) .

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a>Usar componentes roteáveis em um Razor aplicativo de páginas

*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*

Para dar suporte a componentes roteáveis Razor em Razor páginas aplicativos:

1. Siga as orientações na seção [configuração](#configuration) .

1. Adicione um `App.razor` arquivo à raiz do projeto com o seguinte conteúdo:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Adicione um `_Host.cshtml` arquivo à `Pages` pasta com o seguinte conteúdo:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Os componentes usam o `_Layout.cshtml` arquivo compartilhado para seu layout.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura se o `App` componente:

   * É renderizado na página.
   * É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.

   Para obter mais informações sobre o auxiliar de marca de componente, incluindo a passagem de parâmetros e <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuração, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

1. Adicione uma rota de baixa prioridade à `_Host.cshtml` página para a configuração de ponto de extremidade no `Startup.Configure` :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Adicione componentes roteáveis ao aplicativo. Por exemplo:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .

## <a name="use-routable-components-in-an-mvc-app"></a>Usar componentes roteáveis em um aplicativo MVC

*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*

Para dar suporte a componentes roteáveis Razor em aplicativos MVC:

1. Siga as orientações na seção [configuração](#configuration) .

1. Adicione um `App.razor` arquivo à raiz do projeto com o seguinte conteúdo:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Adicione um `_Host.cshtml` arquivo à `Views/Home` pasta com o seguinte conteúdo:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Os componentes usam o `_Layout.cshtml` arquivo compartilhado para seu layout.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura se o `App` componente:

   * É renderizado na página.
   * É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.

   Para obter mais informações sobre o auxiliar de marca de componente, incluindo a passagem de parâmetros e <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuração, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .

1. Adicione uma ação ao controlador Home:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Adicione uma rota de baixa prioridade para a ação do controlador que retorna a `_Host.cshtml` exibição para a configuração do ponto de extremidade no `Startup.Configure` :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Crie uma `Pages` pasta e adicione componentes roteáveis ao aplicativo. Por exemplo:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .

## <a name="render-components-from-a-page-or-view"></a>Renderizar componentes de uma página ou exibição

*Esta seção pertence à adição de componentes a páginas ou exibições, em que os componentes não são roteáveis diretamente das solicitações do usuário.*

Para renderizar um componente de uma página ou exibição, use o [auxiliar de marca do componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).

### <a name="render-stateful-interactive-components"></a>Renderizar componentes interativos com estado

Componentes interativos com estado podem ser adicionados a uma Razor página ou exibição.

Quando a página ou a exibição renderiza:

* O componente é renderizado com a página ou exibição.
* O estado inicial do componente usado para o pré-processamento é perdido.
* O novo estado do componente é criado quando a SignalR conexão é estabelecida.

A página a seguir Razor renderiza um `Counter` componente:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

### <a name="render-noninteractive-components"></a>Renderizar componentes não interativos

Na página a seguir Razor , o `Counter` componente é processado estaticamente com um valor inicial que é especificado usando um formulário. Como o componente é processado estaticamente, o componente não é interativo:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

## <a name="component-namespaces"></a>Namespaces de componente

Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta à página/exibição ou ao `_ViewImports.cshtml` arquivo. No exemplo a seguir:

* Altere `MyAppNamespace` para o namespace do aplicativo.
* Se uma pasta chamada `Components` não for usada para manter os componentes, altere `Components` para a pasta onde residem os componentes.

```cshtml
@using MyAppNamespace.Components
```

O `_ViewImports.cshtml` arquivo está localizado na `Pages` pasta de um Razor aplicativo de páginas ou na `Views` pasta de um aplicativo MVC.

Para obter mais informações, consulte <xref:blazor/components/index#namespaces>.

::: zone-end
