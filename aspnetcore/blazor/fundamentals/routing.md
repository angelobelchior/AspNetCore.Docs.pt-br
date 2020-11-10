---
title: 'Roteamento de ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: Saiba como rotear solicitações em aplicativos e sobre o componente NavLink.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: 8f0aa80d092b6678131a2b7152f21ecb8e168257
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430985"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="aba34-103">Roteamento de ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="aba34-103">ASP.NET Core :::no-loc(Blazor)::: routing</span></span>

<span data-ttu-id="aba34-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="aba34-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="aba34-105">Saiba como rotear solicitações e como usar o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente para criar links de navegação em :::no-loc(Blazor)::: aplicativos.</span><span class="sxs-lookup"><span data-stu-id="aba34-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in :::no-loc(Blazor)::: apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="aba34-106">Integração de roteamento de ponto de extremidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aba34-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="aba34-107">:::no-loc(Blazor Server)::: é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="aba34-107">:::no-loc(Blazor Server)::: is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="aba34-108">Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com o <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.Map:::no-loc(Blazor):::Hub%2A> no `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="aba34-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.Map:::no-loc(Blazor):::Hub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="aba34-109">A configuração mais típica é rotear todas as solicitações para uma :::no-loc(Razor)::: página, que atua como o host para a parte do lado do servidor do :::no-loc(Blazor Server)::: aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aba34-109">The most typical configuration is to route all requests to a :::no-loc(Razor)::: page, which acts as the host for the server-side part of the :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="aba34-110">Por convenção, a página *host* geralmente é denominada `_Host.cshtml` .</span><span class="sxs-lookup"><span data-stu-id="aba34-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="aba34-111">A rota especificada no arquivo de host é chamada de *rota de fallback* porque opera com uma prioridade baixa na correspondência de rota.</span><span class="sxs-lookup"><span data-stu-id="aba34-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="aba34-112">A rota de fallback é considerada quando outras rotas não correspondem.</span><span class="sxs-lookup"><span data-stu-id="aba34-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="aba34-113">Isso permite que o aplicativo use outros controladores e páginas sem interferir no :::no-loc(Blazor Server)::: aplicativo.</span><span class="sxs-lookup"><span data-stu-id="aba34-113">This allows the app to use others controllers and pages without interfering with the :::no-loc(Blazor Server)::: app.</span></span>

<span data-ttu-id="aba34-114">Para obter informações sobre como configurar <xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> a hospedagem de servidor de URL não raiz, consulte <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="aba34-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="aba34-115">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="aba34-115">Route templates</span></span>

<span data-ttu-id="aba34-116">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente permite o roteamento para cada componente com uma rota especificada.</span><span class="sxs-lookup"><span data-stu-id="aba34-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="aba34-117">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente aparece no `App.razor` arquivo:</span><span class="sxs-lookup"><span data-stu-id="aba34-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="aba34-118">Quando um `.razor` arquivo com uma `@page` diretiva é compilado, a classe gerada é fornecida <xref:Microsoft.AspNetCore.Components.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="aba34-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="aba34-119">Em tempo de execução, o <xref:Microsoft.AspNetCore.Components.RouteView> componente:</span><span class="sxs-lookup"><span data-stu-id="aba34-119">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="aba34-120">Recebe o <xref:Microsoft.AspNetCore.Components.RouteData> do <xref:Microsoft.AspNetCore.Components.Routing.Router> junto com os parâmetros desejados.</span><span class="sxs-lookup"><span data-stu-id="aba34-120">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="aba34-121">Renderiza o componente especificado com seu layout (ou um layout padrão opcional) usando os parâmetros especificados.</span><span class="sxs-lookup"><span data-stu-id="aba34-121">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="aba34-122">Opcionalmente, você pode especificar um <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parâmetro com uma classe de layout a ser usada para componentes que não especificam um layout.</span><span class="sxs-lookup"><span data-stu-id="aba34-122">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="aba34-123">Os :::no-loc(Blazor)::: modelos padrão especificam o `MainLayout` componente.</span><span class="sxs-lookup"><span data-stu-id="aba34-123">The default :::no-loc(Blazor)::: templates specify the `MainLayout` component.</span></span> <span data-ttu-id="aba34-124">`MainLayout.razor` está na pasta do projeto de modelo `Shared` .</span><span class="sxs-lookup"><span data-stu-id="aba34-124">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="aba34-125">Para obter mais informações sobre layouts, consulte <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="aba34-125">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="aba34-126">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="aba34-126">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="aba34-127">O componente a seguir responde a solicitações para o `/:::no-loc(Blazor):::Route` e o `/Different:::no-loc(Blazor):::Route` :</span><span class="sxs-lookup"><span data-stu-id="aba34-127">The following component responds to requests for `/:::no-loc(Blazor):::Route` and `/Different:::no-loc(Blazor):::Route`:</span></span>

```razor
@page "/:::no-loc(Blazor):::Route"
@page "/Different:::no-loc(Blazor):::Route"

<h1>:::no-loc(Blazor)::: routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="aba34-128">Para que as URLs sejam resolvidas corretamente, o aplicativo deve incluir uma `<base>` marca em seu `wwwroot/index.html` arquivo ( :::no-loc(Blazor WebAssembly)::: ) ou `Pages/_Host.cshtml` arquivo ( :::no-loc(Blazor Server)::: ) com o caminho base do aplicativo especificado no `href` atributo ( `<base href="/">` ).</span><span class="sxs-lookup"><span data-stu-id="aba34-128">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="aba34-129">Para obter mais informações, consulte <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="aba34-129">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="aba34-130">Fornecer conteúdo personalizado quando o conteúdo não for encontrado</span><span class="sxs-lookup"><span data-stu-id="aba34-130">Provide custom content when content isn't found</span></span>

<span data-ttu-id="aba34-131">O <xref:Microsoft.AspNetCore.Components.Routing.Router> componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.</span><span class="sxs-lookup"><span data-stu-id="aba34-131">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="aba34-132">No `App.razor` arquivo, defina conteúdo personalizado no <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> parâmetro de modelo do <xref:Microsoft.AspNetCore.Components.Routing.Router> componente:</span><span class="sxs-lookup"><span data-stu-id="aba34-132">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="aba34-133">O conteúdo das `<NotFound>` marcas pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="aba34-133">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="aba34-134">Para aplicar um layout padrão ao <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> conteúdo, consulte <xref:blazor/layouts> .</span><span class="sxs-lookup"><span data-stu-id="aba34-134">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="aba34-135">Rotear para componentes de vários assemblies</span><span class="sxs-lookup"><span data-stu-id="aba34-135">Route to components from multiple assemblies</span></span>

<span data-ttu-id="aba34-136">Use o <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parâmetro para especificar assemblies adicionais para o <xref:Microsoft.AspNetCore.Components.Routing.Router> componente a ser considerado ao procurar componentes roteáveis.</span><span class="sxs-lookup"><span data-stu-id="aba34-136">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="aba34-137">Os assemblies especificados são considerados além do `AppAssembly` assembly especificado.</span><span class="sxs-lookup"><span data-stu-id="aba34-137">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="aba34-138">No exemplo a seguir, `Component1` é um componente roteável definido em uma biblioteca de classes referenciada.</span><span class="sxs-lookup"><span data-stu-id="aba34-138">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="aba34-139">O exemplo a seguir <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> resulta no suporte de roteamento para `Component1` :</span><span class="sxs-lookup"><span data-stu-id="aba34-139">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="aba34-140">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="aba34-140">Route parameters</span></span>

<span data-ttu-id="aba34-141">O roteador usa parâmetros de rota para popular os parâmetros de componente correspondentes com o mesmo nome (não diferencia maiúsculas de minúsculas):</span><span class="sxs-lookup"><span data-stu-id="aba34-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>:::no-loc(Blazor)::: is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="aba34-142">Não há suporte para parâmetros opcionais.</span><span class="sxs-lookup"><span data-stu-id="aba34-142">Optional parameters aren't supported.</span></span> <span data-ttu-id="aba34-143">Duas `@page` diretivas são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="aba34-143">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="aba34-144">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="aba34-144">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="aba34-145">A segunda `@page` diretiva usa o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="aba34-145">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="aba34-146">Restrições de rota</span><span class="sxs-lookup"><span data-stu-id="aba34-146">Route constraints</span></span>

<span data-ttu-id="aba34-147">Uma restrição de rota impõe a correspondência de tipo em um segmento de rota para um componente.</span><span class="sxs-lookup"><span data-stu-id="aba34-147">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="aba34-148">No exemplo a seguir, a rota para o `Users` componente só corresponde se:</span><span class="sxs-lookup"><span data-stu-id="aba34-148">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="aba34-149">Um `Id` segmento de rota está presente na URL da solicitação.</span><span class="sxs-lookup"><span data-stu-id="aba34-149">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="aba34-150">O `Id` segmento é um inteiro ( `int` ).</span><span class="sxs-lookup"><span data-stu-id="aba34-150">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="aba34-151">As restrições de rota mostradas na tabela a seguir estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="aba34-151">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="aba34-152">Para as restrições de rota que correspondem à cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="aba34-152">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="aba34-153">Constraint</span><span class="sxs-lookup"><span data-stu-id="aba34-153">Constraint</span></span> | <span data-ttu-id="aba34-154">Exemplo</span><span class="sxs-lookup"><span data-stu-id="aba34-154">Example</span></span>           | <span data-ttu-id="aba34-155">Correspondências de exemplo</span><span class="sxs-lookup"><span data-stu-id="aba34-155">Example Matches</span></span>                                                                  | <span data-ttu-id="aba34-156">Constante</span><span class="sxs-lookup"><span data-stu-id="aba34-156">Invariant</span></span><br><span data-ttu-id="aba34-157">culture</span><span class="sxs-lookup"><span data-stu-id="aba34-157">culture</span></span><br><span data-ttu-id="aba34-158">correspondência</span><span class="sxs-lookup"><span data-stu-id="aba34-158">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="aba34-159">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="aba34-159">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="aba34-160">No</span><span class="sxs-lookup"><span data-stu-id="aba34-160">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="aba34-161">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="aba34-161">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="aba34-162">Yes</span><span class="sxs-lookup"><span data-stu-id="aba34-162">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="aba34-163">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="aba34-163">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="aba34-164">Yes</span><span class="sxs-lookup"><span data-stu-id="aba34-164">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="aba34-165">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="aba34-165">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="aba34-166">Yes</span><span class="sxs-lookup"><span data-stu-id="aba34-166">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="aba34-167">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="aba34-167">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="aba34-168">Yes</span><span class="sxs-lookup"><span data-stu-id="aba34-168">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="aba34-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="aba34-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="aba34-170">No</span><span class="sxs-lookup"><span data-stu-id="aba34-170">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="aba34-171">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="aba34-171">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="aba34-172">Yes</span><span class="sxs-lookup"><span data-stu-id="aba34-172">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="aba34-173">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="aba34-173">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="aba34-174">Yes</span><span class="sxs-lookup"><span data-stu-id="aba34-174">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="aba34-175">As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou <xref:System.DateTime>) sempre usam a cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="aba34-175">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="aba34-176">Essas restrições consideram que a URL não é localizável.</span><span class="sxs-lookup"><span data-stu-id="aba34-176">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="aba34-177">Roteamento com URLs que contêm pontos</span><span class="sxs-lookup"><span data-stu-id="aba34-177">Routing with URLs that contain dots</span></span>

<span data-ttu-id="aba34-178">Para hospedado :::no-loc(Blazor WebAssembly)::: e :::no-loc(Blazor Server)::: aplicativos, o modelo de rota padrão do lado do servidor pressupõe que se o último segmento de uma URL de solicitação contiver um ponto ( `.` ) de que um arquivo é solicitado (por exemplo, `https://localhost.com:5001/example/some.thing` ).</span><span class="sxs-lookup"><span data-stu-id="aba34-178">For hosted :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server)::: apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested (for example, `https://localhost.com:5001/example/some.thing`).</span></span> <span data-ttu-id="aba34-179">Sem configuração adicional, um aplicativo retornará uma resposta *404-não encontrada* se isso tiver sido destinado a rotear para um componente.</span><span class="sxs-lookup"><span data-stu-id="aba34-179">Without additional configuration, an app returns a *404 - Not Found* response if this was meant to route to a component.</span></span> <span data-ttu-id="aba34-180">Para usar uma rota com um ou mais parâmetros que contenham um ponto, o aplicativo deve configurar a rota com um modelo personalizado.</span><span class="sxs-lookup"><span data-stu-id="aba34-180">To use a route with one or more parameters that contains a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="aba34-181">Considere o seguinte `Example` componente que pode receber um parâmetro de rota do último segmento da URL:</span><span class="sxs-lookup"><span data-stu-id="aba34-181">Consider the following `Example` component that can receive a route parameter from the last segment of the URL:</span></span>

```razor
@page "/example"
@page "/example/{param}"

<p>
    Param: @Param
</p>

@code {
    [Parameter]
    public string Param { get; set; }
}
```

<span data-ttu-id="aba34-182">Para permitir que o aplicativo de *servidor* de uma solução hospedada :::no-loc(Blazor WebAssembly)::: encaminhe a solicitação com um ponto no `param` parâmetro, adicione um modelo de rota de arquivo de fallback com o parâmetro opcional em `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="aba34-182">To permit the *Server* app of a hosted :::no-loc(Blazor WebAssembly)::: solution to route the request with a dot in the `param` parameter, add a fallback file route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="aba34-183">Para configurar um :::no-loc(Blazor Server)::: aplicativo para rotear a solicitação com um ponto no `param` parâmetro, adicione um modelo de rota de página de fallback com o parâmetro opcional em `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="aba34-183">To configure a :::no-loc(Blazor Server)::: app to route the request with a dot in the `param` parameter, add a fallback page route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="aba34-184">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="aba34-184">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="aba34-185">Capturar todos os parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="aba34-185">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="aba34-186">*Esta seção se aplica a ASP.NET Core no .NET 5 Release Candidate 1 (RC1) ou posterior.*</span><span class="sxs-lookup"><span data-stu-id="aba34-186">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="aba34-187">Catch-todos os parâmetros de rota, que capturam caminhos entre vários limites de pasta, têm suporte em componentes.</span><span class="sxs-lookup"><span data-stu-id="aba34-187">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="aba34-188">O parâmetro de rota catch-all deve ser:</span><span class="sxs-lookup"><span data-stu-id="aba34-188">The catch-all route parameter must be:</span></span>

* <span data-ttu-id="aba34-189">Chamado para corresponder ao nome do segmento de rota.</span><span class="sxs-lookup"><span data-stu-id="aba34-189">Named to match the route segment name.</span></span> <span data-ttu-id="aba34-190">A nomeação não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="aba34-190">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="aba34-191">Um tipo `string`.</span><span class="sxs-lookup"><span data-stu-id="aba34-191">A `string` type.</span></span> <span data-ttu-id="aba34-192">A estrutura não fornece a conversão automática.</span><span class="sxs-lookup"><span data-stu-id="aba34-192">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="aba34-193">No final da URL.</span><span class="sxs-lookup"><span data-stu-id="aba34-193">At the end of the URL.</span></span>

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

<span data-ttu-id="aba34-194">Para a URL `/page/this/is/a/test` com um modelo de rota de `/page/{*pageRoute}` , o valor de `PageRoute` é definido como `this/is/a/test` .</span><span class="sxs-lookup"><span data-stu-id="aba34-194">For the URL `/page/this/is/a/test` with a route template of `/page/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="aba34-195">As barras e os segmentos do caminho capturado são decodificados.</span><span class="sxs-lookup"><span data-stu-id="aba34-195">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="aba34-196">Para um modelo de rota do `/page/{*pageRoute}` , a URL `/page/this/is/a%2Ftest%2A` produz `this/is/a/test*` .</span><span class="sxs-lookup"><span data-stu-id="aba34-196">For a route template of `/page/{*pageRoute}`, the URL `/page/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="aba34-197">Catch-todos os parâmetros de rota têm suporte no ASP.NET Core 5,0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="aba34-197">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span>

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="aba34-198">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="aba34-198">NavLink component</span></span>

<span data-ttu-id="aba34-199">Use um <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente no lugar de elementos de hiperlink HTML ( `<a>` ) ao criar links de navegação.</span><span class="sxs-lookup"><span data-stu-id="aba34-199">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="aba34-200">Um <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente se comporta como um `<a>` elemento, exceto que alterna uma `active` classe CSS com base no fato de sua `href` correspondência com a URL atual.</span><span class="sxs-lookup"><span data-stu-id="aba34-200">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="aba34-201">A `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.</span><span class="sxs-lookup"><span data-stu-id="aba34-201">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="aba34-202">Opcionalmente, atribua um nome de classe CSS ao <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> para aplicar uma classe CSS personalizada ao link renderizado quando a rota atual corresponder ao `href` .</span><span class="sxs-lookup"><span data-stu-id="aba34-202">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="aba34-203">O componente a seguir `NavMenu` cria uma [`Bootstrap`](https://getbootstrap.com/docs/) barra de navegação que demonstra como usar <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componentes:</span><span class="sxs-lookup"><span data-stu-id="aba34-203">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="aba34-204">Há duas <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> opções que você pode atribuir ao `Match` atributo do `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="aba34-204">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="aba34-205"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: O <xref:Microsoft.AspNetCore.Components.Routing.NavLink> está ativo quando corresponde à URL atual inteira.</span><span class="sxs-lookup"><span data-stu-id="aba34-205"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="aba34-206"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> ( *padrão* ): o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> está ativo quando ele corresponde a qualquer prefixo da URL atual.</span><span class="sxs-lookup"><span data-stu-id="aba34-206"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> ( *default* ): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="aba34-207">No exemplo anterior, a página inicial <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` corresponde à URL inicial e recebe apenas a `active` classe CSS na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/` ).</span><span class="sxs-lookup"><span data-stu-id="aba34-207">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="aba34-208">O segundo <xref:Microsoft.AspNetCore.Components.Routing.NavLink> recebe a `active` classe quando o usuário visita qualquer URL com um `MyComponent` prefixo (por exemplo, `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment` ).</span><span class="sxs-lookup"><span data-stu-id="aba34-208">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="aba34-209"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Atributos de componente adicionais são passados para a marca de âncora renderizada.</span><span class="sxs-lookup"><span data-stu-id="aba34-209">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="aba34-210">No exemplo a seguir, o <xref:Microsoft.AspNetCore.Components.Routing.NavLink> componente inclui o `target` atributo:</span><span class="sxs-lookup"><span data-stu-id="aba34-210">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="aba34-211">A seguinte marcação HTML é renderizada:</span><span class="sxs-lookup"><span data-stu-id="aba34-211">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="aba34-212">Devido à maneira que :::no-loc(Blazor)::: renderiza conteúdo filho, `NavLink` os componentes de renderização dentro de um `for` loop exigirão uma variável de índice local se a variável de loop de incremento for usada no `NavLink` conteúdo do componente (filho):</span><span class="sxs-lookup"><span data-stu-id="aba34-212">Due to the way that :::no-loc(Blazor)::: renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="aba34-213">Usar uma variável de índice nesse cenário é um requisito para **qualquer** componente filho que usa uma variável de loop em seu [conteúdo filho](xref:blazor/components/index#child-content), não apenas o `NavLink` componente.</span><span class="sxs-lookup"><span data-stu-id="aba34-213">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="aba34-214">Como alternativa, use um `foreach` loop com <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="aba34-214">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="aba34-215">Auxiliares de URI e estado de navegação</span><span class="sxs-lookup"><span data-stu-id="aba34-215">URI and navigation state helpers</span></span>

<span data-ttu-id="aba34-216">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> para trabalhar com URIs e navegação em código C#.</span><span class="sxs-lookup"><span data-stu-id="aba34-216">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="aba34-217"><xref:Microsoft.AspNetCore.Components.NavigationManager> fornece o evento e os métodos mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="aba34-217"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="aba34-218">Membro</span><span class="sxs-lookup"><span data-stu-id="aba34-218">Member</span></span> | <span data-ttu-id="aba34-219">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="aba34-219">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="aba34-220">Obtém o URI absoluto atual.</span><span class="sxs-lookup"><span data-stu-id="aba34-220">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="aba34-221">Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="aba34-221">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="aba34-222">Normalmente, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponde ao `href` atributo no elemento do documento `<base>` em `wwwroot/index.html` ( :::no-loc(Blazor WebAssembly)::: ) ou `Pages/_Host.cshtml` ( :::no-loc(Blazor Server)::: ).</span><span class="sxs-lookup"><span data-stu-id="aba34-222">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="aba34-223">Navega para o URI especificado.</span><span class="sxs-lookup"><span data-stu-id="aba34-223">Navigates to the specified URI.</span></span> <span data-ttu-id="aba34-224">Se `forceLoad` for `true` :</span><span class="sxs-lookup"><span data-stu-id="aba34-224">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="aba34-225">O roteamento do lado do cliente é ignorado.</span><span class="sxs-lookup"><span data-stu-id="aba34-225">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="aba34-226">O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="aba34-226">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="aba34-227">Um evento que é acionado quando o local de navegação é alterado.</span><span class="sxs-lookup"><span data-stu-id="aba34-227">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="aba34-228">Converte um URI relativo em um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="aba34-228">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="aba34-229">Dado um URI de base (por exemplo, um URI retornado anteriormente pelo <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> ), converte um URI absoluto em um URI relativo ao prefixo de URI de base.</span><span class="sxs-lookup"><span data-stu-id="aba34-229">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="aba34-230">O componente a seguir navega para o componente do aplicativo `Counter` quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="aba34-230">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="aba34-231">O componente a seguir manipula um evento de alteração de local assinando o <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="aba34-231">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="aba34-232">O `HandleLocationChanged` método é desvinculado quando `Dispose` é chamado pelo Framework.</span><span class="sxs-lookup"><span data-stu-id="aba34-232">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="aba34-233">A desconexão do método permite a coleta de lixo do componente.</span><span class="sxs-lookup"><span data-stu-id="aba34-233">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="aba34-234"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> fornece as seguintes informações sobre o evento:</span><span class="sxs-lookup"><span data-stu-id="aba34-234"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="aba34-235"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: A URL do novo local.</span><span class="sxs-lookup"><span data-stu-id="aba34-235"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="aba34-236"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Se `true` , :::no-loc(Blazor)::: interceptou a navegação do navegador.</span><span class="sxs-lookup"><span data-stu-id="aba34-236"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, :::no-loc(Blazor)::: intercepted the navigation from the browser.</span></span> <span data-ttu-id="aba34-237">Se `false` , <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> fez com que a navegação ocorresse.</span><span class="sxs-lookup"><span data-stu-id="aba34-237">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="aba34-238">Para obter mais informações sobre a disposição de componentes, consulte <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="aba34-238">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="aba34-239">Cadeia de caracteres de consulta e parâmetros de análise</span><span class="sxs-lookup"><span data-stu-id="aba34-239">Query string and parse parameters</span></span>

<span data-ttu-id="aba34-240">A cadeia de caracteres de consulta de uma solicitação pode ser obtida da <xref:Microsoft.AspNetCore.Components.NavigationManager> <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> Propriedade do:</span><span class="sxs-lookup"><span data-stu-id="aba34-240">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="aba34-241">Para analisar os parâmetros de uma cadeia de caracteres de consulta:</span><span class="sxs-lookup"><span data-stu-id="aba34-241">To parse a query string's parameters:</span></span>

* <span data-ttu-id="aba34-242">Adicione uma referência de pacote para [Microsoft. AspNetCore. webutilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span><span class="sxs-lookup"><span data-stu-id="aba34-242">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="aba34-243">Obtenha o valor depois de analisar a cadeia de caracteres de consulta com <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="aba34-243">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

<span data-ttu-id="aba34-244">O espaço reservado `{KEY}` no exemplo anterior é a chave de parâmetro da cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="aba34-244">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="aba34-245">Por exemplo, o par chave-valor de URL `?ship=Tardis` usa uma chave de `ship` .</span><span class="sxs-lookup"><span data-stu-id="aba34-245">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
