---
title: 'PreRender e integre componentes de ASP.NET Core :::no-loc(Razor):::'
author: guardrex
description: 'Saiba mais sobre :::no-loc(Razor)::: cenários de integração de componentes para :::no-loc(Blazor)::: aplicativos, incluindo a renderização de :::no-loc(Razor)::: componentes no servidor.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: affca6c9b585b91787f94a13144d07bedfefdd37
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431698"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a><span data-ttu-id="e77c7-103">PreRender e integre componentes de ASP.NET Core :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="e77c7-103">Prerender and integrate ASP.NET Core :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="e77c7-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e77c7-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e77c7-105">:::no-loc(Razor)::: os componentes podem ser integrados em :::no-loc(Razor)::: páginas e aplicativos MVC em uma solução hospedada :::no-loc(Blazor WebAssembly)::: .</span><span class="sxs-lookup"><span data-stu-id="e77c7-105">:::no-loc(Razor)::: components can be integrated into :::no-loc(Razor)::: Pages and MVC apps in a hosted :::no-loc(Blazor WebAssembly)::: solution.</span></span> <span data-ttu-id="e77c7-106">Quando a página ou a exibição é renderizada, os componentes podem ser renderizados ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="e77c7-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="e77c7-107">Configuração</span><span class="sxs-lookup"><span data-stu-id="e77c7-107">Configuration</span></span>

<span data-ttu-id="e77c7-108">Para configurar o pré-processamento para um :::no-loc(Blazor WebAssembly)::: aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e77c7-108">To set up prerendering for a :::no-loc(Blazor WebAssembly)::: app:</span></span>

1. <span data-ttu-id="e77c7-109">Hospede o :::no-loc(Blazor WebAssembly)::: aplicativo em um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e77c7-109">Host the :::no-loc(Blazor WebAssembly)::: app in an ASP.NET Core app.</span></span> <span data-ttu-id="e77c7-110">Um :::no-loc(Blazor WebAssembly)::: aplicativo autônomo pode ser adicionado a uma solução de ASP.NET Core, ou você pode usar um :::no-loc(Blazor WebAssembly)::: aplicativo hospedado criado a partir do :::no-loc(Blazor)::: modelo de projeto hospedado.</span><span class="sxs-lookup"><span data-stu-id="e77c7-110">A standalone :::no-loc(Blazor WebAssembly)::: app can be added to an ASP.NET Core solution, or you can use a hosted :::no-loc(Blazor WebAssembly)::: app created from the :::no-loc(Blazor)::: Hosted project template.</span></span>

1. <span data-ttu-id="e77c7-111">Remova o arquivo estático padrão `wwwroot/index.html` do :::no-loc(Blazor WebAssembly)::: projeto do cliente.</span><span class="sxs-lookup"><span data-stu-id="e77c7-111">Remove the default static `wwwroot/index.html` file from the :::no-loc(Blazor WebAssembly)::: client project.</span></span>

1. <span data-ttu-id="e77c7-112">Exclua a seguinte linha no `Program.Main` projeto do cliente:</span><span class="sxs-lookup"><span data-stu-id="e77c7-112">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="e77c7-113">Adicione um `Pages/_Host.cshtml` arquivo ao projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="e77c7-113">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="e77c7-114">Você pode obter um `_Host.cshtml` arquivo de um aplicativo criado a partir do :::no-loc(Blazor Server)::: modelo com o `dotnet new blazorserver -o :::no-loc(Blazor):::Server` comando em um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="e77c7-114">You can obtain a `_Host.cshtml` file from an app created from the :::no-loc(Blazor Server)::: template with the `dotnet new blazorserver -o :::no-loc(Blazor):::Server` command in a command shell.</span></span> <span data-ttu-id="e77c7-115">Depois de colocar o `Pages/_Host.cshtml` arquivo no aplicativo de servidor da solução hospedada :::no-loc(Blazor WebAssembly)::: , faça as seguintes alterações no arquivo:</span><span class="sxs-lookup"><span data-stu-id="e77c7-115">After placing the `Pages/_Host.cshtml` file into the server app of the hosted :::no-loc(Blazor WebAssembly)::: solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="e77c7-116">Defina o namespace para a pasta do aplicativo do servidor `Pages` (por exemplo, `@namespace :::no-loc(Blazor):::Hosted.Server.Pages` ).</span><span class="sxs-lookup"><span data-stu-id="e77c7-116">Set the namespace to the server app's `Pages` folder (for example, `@namespace :::no-loc(Blazor):::Hosted.Server.Pages`).</span></span>
   * <span data-ttu-id="e77c7-117">Defina uma [`@using`](xref:mvc/views/razor#using) diretiva para o projeto cliente (por exemplo, `@using :::no-loc(Blazor):::Hosted.Client` ).</span><span class="sxs-lookup"><span data-stu-id="e77c7-117">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using :::no-loc(Blazor):::Hosted.Client`).</span></span>
   * <span data-ttu-id="e77c7-118">Atualize os links de folha de estilos para apontar para a folha de estilos do aplicativo Webassembly.</span><span class="sxs-lookup"><span data-stu-id="e77c7-118">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="e77c7-119">No exemplo a seguir, o namespace do aplicativo cliente é `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="e77c7-119">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href=":::no-loc(Blazor):::Hosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="e77c7-120">Atualize o `render-mode` do [auxiliar de marca do componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para PreRender o `App` componente raiz:</span><span class="sxs-lookup"><span data-stu-id="e77c7-120">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="e77c7-121">Atualize a :::no-loc(Blazor)::: origem do script para usar o script do lado do cliente :::no-loc(Blazor WebAssembly)::: :</span><span class="sxs-lookup"><span data-stu-id="e77c7-121">Update the :::no-loc(Blazor)::: script source to use the client-side :::no-loc(Blazor WebAssembly)::: script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="e77c7-122">No `Startup.Configure` ( `Startup.cs` ) do projeto do servidor:</span><span class="sxs-lookup"><span data-stu-id="e77c7-122">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="e77c7-123">Chame `UseDeveloperExceptionPage` no app Builder no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="e77c7-123">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="e77c7-124">Chame `Use:::no-loc(Blazor):::FrameworkFiles` no app Builder.</span><span class="sxs-lookup"><span data-stu-id="e77c7-124">Call `Use:::no-loc(Blazor):::FrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="e77c7-125">Altere o fallback da `index.html` página ( `endpoints.MapFallbackToFile("index.html");` ) para a `_Host.cshtml` página.</span><span class="sxs-lookup"><span data-stu-id="e77c7-125">Change the fallback from the `index.html` page (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page.</span></span>

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
       app.Use:::no-loc(Blazor):::FrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.Map:::no-loc(Razor):::Pages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="e77c7-126">Renderizar componentes em uma página ou exibição com o auxiliar de marca do componente</span><span class="sxs-lookup"><span data-stu-id="e77c7-126">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="e77c7-127">O auxiliar de marca de componente dá suporte a dois modos de renderização para renderizar um componente de um :::no-loc(Blazor WebAssembly)::: aplicativo em uma página ou exibição:</span><span class="sxs-lookup"><span data-stu-id="e77c7-127">The Component Tag Helper supports two render modes for rendering a component from a :::no-loc(Blazor WebAssembly)::: app in a page or view:</span></span>

* <span data-ttu-id="e77c7-128">`WebAssembly`: Renderiza um marcador para um :::no-loc(Blazor WebAssembly)::: aplicativo para usar para incluir um componente interativo quando carregado no navegador.</span><span class="sxs-lookup"><span data-stu-id="e77c7-128">`WebAssembly`: Renders a marker for a :::no-loc(Blazor WebAssembly)::: app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="e77c7-129">O componente não é renderizado.</span><span class="sxs-lookup"><span data-stu-id="e77c7-129">The component isn't prerendered.</span></span> <span data-ttu-id="e77c7-130">Essa opção torna mais fácil renderizar diferentes :::no-loc(Blazor WebAssembly)::: componentes em páginas diferentes.</span><span class="sxs-lookup"><span data-stu-id="e77c7-130">This option makes it easier to render different :::no-loc(Blazor WebAssembly)::: components on different pages.</span></span>
* <span data-ttu-id="e77c7-131">`WebAssemblyPrerendered`: Coloca o componente em HTML estático e inclui um marcador para um :::no-loc(Blazor WebAssembly)::: aplicativo para uso posterior para tornar o componente interativo quando carregado no navegador.</span><span class="sxs-lookup"><span data-stu-id="e77c7-131">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a :::no-loc(Blazor WebAssembly)::: app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="e77c7-132">No exemplo de :::no-loc(Razor)::: páginas a seguir, o `Counter` componente é renderizado em uma página.</span><span class="sxs-lookup"><span data-stu-id="e77c7-132">In the following :::no-loc(Razor)::: Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="e77c7-133">Para tornar o componente interativo, o :::no-loc(Blazor WebAssembly)::: script é incluído na [seção renderizar](xref:mvc/views/layout#sections)da página.</span><span class="sxs-lookup"><span data-stu-id="e77c7-133">To make the component interactive, the :::no-loc(Blazor WebAssembly)::: script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="e77c7-134">Para evitar o uso do namespace completo para o `Counter` componente com o auxiliar de marca de componente ( `{APP ASSEMBLY}.Pages.Counter` ), adicione uma [`@using`](xref:mvc/views/razor#using) diretiva para o namespace do aplicativo cliente `Pages` .</span><span class="sxs-lookup"><span data-stu-id="e77c7-134">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="e77c7-135">No exemplo a seguir, o namespace do aplicativo cliente é `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="e77c7-135">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```cshtml
...
@using :::no-loc(Blazor):::Hosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="e77c7-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="e77c7-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="e77c7-137">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="e77c7-137">Is prerendered into the page.</span></span>
* <span data-ttu-id="e77c7-138">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um :::no-loc(Blazor)::: aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="e77c7-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

<span data-ttu-id="e77c7-139">Para obter mais informações sobre o auxiliar de marca de componente, incluindo a passagem de parâmetros e <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuração, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="e77c7-139">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="e77c7-140">O exemplo anterior requer que o layout do aplicativo do servidor ( `_Layout.cshtml` ) inclua uma [seção render](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A> ) para o script dentro da `</body>` marca de fechamento:</span><span class="sxs-lookup"><span data-stu-id="e77c7-140">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="e77c7-141">O `_Layout.cshtml` arquivo está localizado na `Pages/Shared` pasta em um :::no-loc(Razor)::: aplicativo ou pasta de páginas `Views/Shared` em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="e77c7-141">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a :::no-loc(Razor)::: Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="e77c7-142">Se o aplicativo também deve estilizar componentes com os estilos no :::no-loc(Blazor WebAssembly)::: aplicativo, inclua os estilos do aplicativo no `_Layout.cshtml` arquivo.</span><span class="sxs-lookup"><span data-stu-id="e77c7-142">If the app should also style components with the styles in the :::no-loc(Blazor WebAssembly)::: app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="e77c7-143">No exemplo a seguir, o namespace do aplicativo cliente é `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="e77c7-143">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href=":::no-loc(Blazor):::Hosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="e77c7-144">Renderizar componentes em uma página ou exibição com um seletor de CSS</span><span class="sxs-lookup"><span data-stu-id="e77c7-144">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="e77c7-145">Adicione componentes raiz ao projeto do *cliente* no `Program.Main` ( `Program.cs` ).</span><span class="sxs-lookup"><span data-stu-id="e77c7-145">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="e77c7-146">No exemplo a seguir, o `Counter` componente é declarado como um componente raiz com um seletor CSS que seleciona o elemento com o `id` que corresponde a `my-counter` .</span><span class="sxs-lookup"><span data-stu-id="e77c7-146">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="e77c7-147">No exemplo a seguir, o namespace do aplicativo cliente é `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="e77c7-147">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```csharp
using :::no-loc(Blazor):::Hosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="e77c7-148">No exemplo de :::no-loc(Razor)::: páginas a seguir, o `Counter` componente é renderizado em uma página.</span><span class="sxs-lookup"><span data-stu-id="e77c7-148">In the following :::no-loc(Razor)::: Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="e77c7-149">Para tornar o componente interativo, o :::no-loc(Blazor WebAssembly)::: script é incluído na [seção renderizar](xref:mvc/views/layout#sections)da página:</span><span class="sxs-lookup"><span data-stu-id="e77c7-149">To make the component interactive, the :::no-loc(Blazor WebAssembly)::: script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="e77c7-150">O exemplo anterior requer que o layout do aplicativo do servidor ( `_Layout.cshtml` ) inclua uma [seção render](xref:mvc/views/layout#sections) ( <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A> ) para o script dentro da `</body>` marca de fechamento:</span><span class="sxs-lookup"><span data-stu-id="e77c7-150">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="e77c7-151">O `_Layout.cshtml` arquivo está localizado na `Pages/Shared` pasta em um :::no-loc(Razor)::: aplicativo ou pasta de páginas `Views/Shared` em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="e77c7-151">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a :::no-loc(Razor)::: Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="e77c7-152">Se o aplicativo também deve estilizar componentes com os estilos no :::no-loc(Blazor WebAssembly)::: aplicativo, inclua os estilos do aplicativo no `_Layout.cshtml` arquivo.</span><span class="sxs-lookup"><span data-stu-id="e77c7-152">If the app should also style components with the styles in the :::no-loc(Blazor WebAssembly)::: app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="e77c7-153">No exemplo a seguir, o namespace do aplicativo cliente é `:::no-loc(Blazor):::Hosted.Client` :</span><span class="sxs-lookup"><span data-stu-id="e77c7-153">In the following example, the client app's namespace is `:::no-loc(Blazor):::Hosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href=":::no-loc(Blazor):::Hosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="e77c7-154">A integração de :::no-loc(Razor)::: componentes em :::no-loc(Razor)::: páginas e aplicativos MVC em uma solução hospedada :::no-loc(Blazor WebAssembly)::: tem suporte no ASP.NET Core no .NET 5 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="e77c7-154">Integrating :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps in a hosted :::no-loc(Blazor WebAssembly)::: solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="e77c7-155">Selecione uma versão do .NET 5 ou posterior deste artigo.</span><span class="sxs-lookup"><span data-stu-id="e77c7-155">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="e77c7-156">:::no-loc(Razor)::: os componentes podem ser integrados em :::no-loc(Razor)::: páginas e aplicativos MVC em um :::no-loc(Blazor Server)::: aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e77c7-156">:::no-loc(Razor)::: components can be integrated into :::no-loc(Razor)::: Pages and MVC apps in a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="e77c7-157">Quando a página ou a exibição é renderizada, os componentes podem ser renderizados ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="e77c7-157">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="e77c7-158">Depois de [Configurar o aplicativo](#configuration), use as diretrizes nas seções a seguir, dependendo dos requisitos do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e77c7-158">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="e77c7-159">Componentes roteáveis: para componentes que são roteáveis diretamente das solicitações do usuário.</span><span class="sxs-lookup"><span data-stu-id="e77c7-159">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="e77c7-160">Siga estas diretrizes para que os visitantes possam fazer uma solicitação HTTP em seu navegador para um componente com uma [`@page`](xref:mvc/views/razor#page) diretiva.</span><span class="sxs-lookup"><span data-stu-id="e77c7-160">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="e77c7-161">Usar componentes roteáveis em um :::no-loc(Razor)::: aplicativo de páginas</span><span class="sxs-lookup"><span data-stu-id="e77c7-161">Use routable components in a :::no-loc(Razor)::: Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="e77c7-162">Usar componentes roteáveis em um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="e77c7-162">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="e77c7-163">[Renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view): para componentes que não são roteáveis diretamente das solicitações do usuário.</span><span class="sxs-lookup"><span data-stu-id="e77c7-163">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="e77c7-164">Siga estas diretrizes quando o aplicativo inserir componentes em páginas e exibições existentes com o [auxiliar de marca de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e77c7-164">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="e77c7-165">Configuração</span><span class="sxs-lookup"><span data-stu-id="e77c7-165">Configuration</span></span>

<span data-ttu-id="e77c7-166">Uma :::no-loc(Razor)::: página existente ou um aplicativo MVC pode integrar :::no-loc(Razor)::: componentes em páginas e exibições:</span><span class="sxs-lookup"><span data-stu-id="e77c7-166">An existing :::no-loc(Razor)::: Pages or MVC app can integrate :::no-loc(Razor)::: components into pages and views:</span></span>

1. <span data-ttu-id="e77c7-167">No arquivo de layout do aplicativo ( `_Layout.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="e77c7-167">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="e77c7-168">Adicione a seguinte `<base>` marcação ao `<head>` elemento:</span><span class="sxs-lookup"><span data-stu-id="e77c7-168">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="e77c7-169">O `href` valor (o *caminho base do aplicativo* ) no exemplo anterior pressupõe que o aplicativo reside no caminho da URL raiz ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="e77c7-169">The `href` value (the *app base path* ) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="e77c7-170">Se o aplicativo for um subaplicativo, siga as orientações na seção *caminho base do aplicativo* do <xref:blazor/host-and-deploy/index#app-base-path> artigo.</span><span class="sxs-lookup"><span data-stu-id="e77c7-170">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="e77c7-171">O `_Layout.cshtml` arquivo está localizado na `Pages/Shared` pasta em um :::no-loc(Razor)::: aplicativo ou pasta de páginas `Views/Shared` em um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="e77c7-171">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a :::no-loc(Razor)::: Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="e77c7-172">Adicione uma `<script>` marca para o `blazor.server.js` script imediatamente antes da `Scripts` seção render:</span><span class="sxs-lookup"><span data-stu-id="e77c7-172">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="e77c7-173">A estrutura adiciona o `blazor.server.js` script ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e77c7-173">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="e77c7-174">Não é necessário adicionar manualmente o script ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e77c7-174">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="e77c7-175">Adicione um `_Imports.razor` arquivo à pasta raiz do projeto com o seguinte conteúdo (altere o último namespace, `MyAppNamespace` , para o namespace do aplicativo):</span><span class="sxs-lookup"><span data-stu-id="e77c7-175">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="e77c7-176">No `Startup.ConfigureServices` , registre o :::no-loc(Blazor Server)::: serviço:</span><span class="sxs-lookup"><span data-stu-id="e77c7-176">In `Startup.ConfigureServices`, register the :::no-loc(Blazor Server)::: service:</span></span>

   ```csharp
   services.AddServerSide:::no-loc(Blazor):::();
   ```

1. <span data-ttu-id="e77c7-177">No `Startup.Configure` , adicione o :::no-loc(Blazor)::: ponto de extremidade do hub a `app.UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="e77c7-177">In `Startup.Configure`, add the :::no-loc(Blazor)::: Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.Map:::no-loc(Blazor):::Hub();
   ```

1. <span data-ttu-id="e77c7-178">Integre componentes em qualquer página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="e77c7-178">Integrate components into any page or view.</span></span> <span data-ttu-id="e77c7-179">Para obter mais informações, consulte a seção [renderizar componentes de uma página ou exibição](#render-components-from-a-page-or-view) .</span><span class="sxs-lookup"><span data-stu-id="e77c7-179">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="e77c7-180">Usar componentes roteáveis em um :::no-loc(Razor)::: aplicativo de páginas</span><span class="sxs-lookup"><span data-stu-id="e77c7-180">Use routable components in a :::no-loc(Razor)::: Pages app</span></span>

<span data-ttu-id="e77c7-181">*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="e77c7-181">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="e77c7-182">Para dar suporte a componentes roteáveis :::no-loc(Razor)::: em :::no-loc(Razor)::: páginas aplicativos:</span><span class="sxs-lookup"><span data-stu-id="e77c7-182">To support routable :::no-loc(Razor)::: components in :::no-loc(Razor)::: Pages apps:</span></span>

1. <span data-ttu-id="e77c7-183">Siga as orientações na seção [configuração](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="e77c7-183">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="e77c7-184">Adicione um `App.razor` arquivo à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="e77c7-184">Add an `App.razor` file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="e77c7-185">Adicione um `_Host.cshtml` arquivo à `Pages` pasta com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="e77c7-185">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="e77c7-186">Os componentes usam o `_Layout.cshtml` arquivo compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="e77c7-186">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="e77c7-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura se o `App` componente:</span><span class="sxs-lookup"><span data-stu-id="e77c7-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="e77c7-188">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="e77c7-188">Is prerendered into the page.</span></span>
   * <span data-ttu-id="e77c7-189">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um :::no-loc(Blazor)::: aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="e77c7-189">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

   <span data-ttu-id="e77c7-190">Para obter mais informações sobre o auxiliar de marca de componente, incluindo a passagem de parâmetros e <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuração, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="e77c7-190">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="e77c7-191">Adicione uma rota de baixa prioridade à `_Host.cshtml` página para a configuração de ponto de extremidade no `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e77c7-191">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="e77c7-192">Adicione componentes roteáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e77c7-192">Add routable components to the app.</span></span> <span data-ttu-id="e77c7-193">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e77c7-193">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="e77c7-194">Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="e77c7-194">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="e77c7-195">Usar componentes roteáveis em um aplicativo MVC</span><span class="sxs-lookup"><span data-stu-id="e77c7-195">Use routable components in an MVC app</span></span>

<span data-ttu-id="e77c7-196">*Esta seção refere-se à adição de componentes que são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="e77c7-196">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="e77c7-197">Para dar suporte a componentes roteáveis :::no-loc(Razor)::: em aplicativos MVC:</span><span class="sxs-lookup"><span data-stu-id="e77c7-197">To support routable :::no-loc(Razor)::: components in MVC apps:</span></span>

1. <span data-ttu-id="e77c7-198">Siga as orientações na seção [configuração](#configuration) .</span><span class="sxs-lookup"><span data-stu-id="e77c7-198">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="e77c7-199">Adicione um `App.razor` arquivo à raiz do projeto com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="e77c7-199">Add an `App.razor` file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="e77c7-200">Adicione um `_Host.cshtml` arquivo à `Views/Home` pasta com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="e77c7-200">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="e77c7-201">Os componentes usam o `_Layout.cshtml` arquivo compartilhado para seu layout.</span><span class="sxs-lookup"><span data-stu-id="e77c7-201">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="e77c7-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura se o `App` componente:</span><span class="sxs-lookup"><span data-stu-id="e77c7-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="e77c7-203">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="e77c7-203">Is prerendered into the page.</span></span>
   * <span data-ttu-id="e77c7-204">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um :::no-loc(Blazor)::: aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="e77c7-204">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

   <span data-ttu-id="e77c7-205">Para obter mais informações sobre o auxiliar de marca de componente, incluindo a passagem de parâmetros e <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuração, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> .</span><span class="sxs-lookup"><span data-stu-id="e77c7-205">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="e77c7-206">Adicione uma ação ao controlador Home:</span><span class="sxs-lookup"><span data-stu-id="e77c7-206">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult :::no-loc(Blazor):::()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="e77c7-207">Adicione uma rota de baixa prioridade para a ação do controlador que retorna a `_Host.cshtml` exibição para a configuração do ponto de extremidade no `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="e77c7-207">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController(":::no-loc(Blazor):::", "Home");
   });
   ```

1. <span data-ttu-id="e77c7-208">Crie uma `Pages` pasta e adicione componentes roteáveis ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e77c7-208">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="e77c7-209">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e77c7-209">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="e77c7-210">Para obter mais informações sobre namespaces, consulte a seção [namespaces de componentes](#component-namespaces) .</span><span class="sxs-lookup"><span data-stu-id="e77c7-210">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="e77c7-211">Renderizar componentes de uma página ou exibição</span><span class="sxs-lookup"><span data-stu-id="e77c7-211">Render components from a page or view</span></span>

<span data-ttu-id="e77c7-212">*Esta seção pertence à adição de componentes a páginas ou exibições, em que os componentes não são roteáveis diretamente das solicitações do usuário.*</span><span class="sxs-lookup"><span data-stu-id="e77c7-212">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="e77c7-213">Para renderizar um componente de uma página ou exibição, use o [auxiliar de marca do componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="e77c7-213">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="e77c7-214">Renderizar componentes interativos com estado</span><span class="sxs-lookup"><span data-stu-id="e77c7-214">Render stateful interactive components</span></span>

<span data-ttu-id="e77c7-215">Componentes interativos com estado podem ser adicionados a uma :::no-loc(Razor)::: página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="e77c7-215">Stateful interactive components can be added to a :::no-loc(Razor)::: page or view.</span></span>

<span data-ttu-id="e77c7-216">Quando a página ou a exibição renderiza:</span><span class="sxs-lookup"><span data-stu-id="e77c7-216">When the page or view renders:</span></span>

* <span data-ttu-id="e77c7-217">O componente é renderizado com a página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="e77c7-217">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="e77c7-218">O estado inicial do componente usado para o pré-processamento é perdido.</span><span class="sxs-lookup"><span data-stu-id="e77c7-218">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="e77c7-219">O novo estado do componente é criado quando a :::no-loc(SignalR)::: conexão é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="e77c7-219">New component state is created when the :::no-loc(SignalR)::: connection is established.</span></span>

<span data-ttu-id="e77c7-220">A página a seguir :::no-loc(Razor)::: renderiza um `Counter` componente:</span><span class="sxs-lookup"><span data-stu-id="e77c7-220">The following :::no-loc(Razor)::: page renders a `Counter` component:</span></span>

```cshtml
<h1>My :::no-loc(Razor)::: Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="e77c7-221">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="e77c7-221">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="e77c7-222">Renderizar componentes não interativos</span><span class="sxs-lookup"><span data-stu-id="e77c7-222">Render noninteractive components</span></span>

<span data-ttu-id="e77c7-223">Na página a seguir :::no-loc(Razor)::: , o `Counter` componente é processado estaticamente com um valor inicial que é especificado usando um formulário.</span><span class="sxs-lookup"><span data-stu-id="e77c7-223">In the following :::no-loc(Razor)::: page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="e77c7-224">Como o componente é processado estaticamente, o componente não é interativo:</span><span class="sxs-lookup"><span data-stu-id="e77c7-224">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My :::no-loc(Razor)::: Page</h1>

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

<span data-ttu-id="e77c7-225">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="e77c7-225">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="e77c7-226">Namespaces de componente</span><span class="sxs-lookup"><span data-stu-id="e77c7-226">Component namespaces</span></span>

<span data-ttu-id="e77c7-227">Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta à página/exibição ou ao `_ViewImports.cshtml` arquivo.</span><span class="sxs-lookup"><span data-stu-id="e77c7-227">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="e77c7-228">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="e77c7-228">In the following example:</span></span>

* <span data-ttu-id="e77c7-229">Altere `MyAppNamespace` para o namespace do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e77c7-229">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="e77c7-230">Se uma pasta chamada `Components` não for usada para manter os componentes, altere `Components` para a pasta onde residem os componentes.</span><span class="sxs-lookup"><span data-stu-id="e77c7-230">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="e77c7-231">O `_ViewImports.cshtml` arquivo está localizado na `Pages` pasta de um :::no-loc(Razor)::: aplicativo de páginas ou na `Views` pasta de um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="e77c7-231">The `_ViewImports.cshtml` file is located in the `Pages` folder of a :::no-loc(Razor)::: Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="e77c7-232">Para obter mais informações, consulte <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="e77c7-232">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end
