---
title: ASP.NET Core Blazor globalização e localização
author: guardrex
description: Saiba como tornar Razor os componentes acessíveis para os usuários em várias culturas e linguagens.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/globalization-localization
ms.openlocfilehash: be73b0c1e33a2cd15c9ff0dc51044f9bd48c43fe
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113810"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a><span data-ttu-id="d70dd-103">ASP.NET Core Blazor globalização e localização</span><span class="sxs-lookup"><span data-stu-id="d70dd-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="d70dd-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d70dd-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="d70dd-105">Razor os componentes podem se tornar acessíveis para os usuários em várias culturas e linguagens.</span><span class="sxs-lookup"><span data-stu-id="d70dd-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="d70dd-106">Os seguintes cenários de globalização e localização do .NET estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="d70dd-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="d70dd-107">. Sistema de recursos da rede</span><span class="sxs-lookup"><span data-stu-id="d70dd-107">.NET's resources system</span></span>
* <span data-ttu-id="d70dd-108">Formatação de número e data específicos da cultura</span><span class="sxs-lookup"><span data-stu-id="d70dd-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="d70dd-109">No momento, há suporte para um conjunto limitado de cenários de localização de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d70dd-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="d70dd-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> e <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *têm suporte* em Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="d70dd-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in Blazor apps.</span></span>
* <span data-ttu-id="d70dd-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> , e a localização de anotações de dados são ASP.NET Core cenários MVC e **não têm suporte** em Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="d70dd-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="d70dd-112">Para obter mais informações, consulte <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="d70dd-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="d70dd-113">Globalização</span><span class="sxs-lookup"><span data-stu-id="d70dd-113">Globalization</span></span>

<span data-ttu-id="d70dd-114">Blazor[`@bind`](xref:mvc/views/razor#bind)a funcionalidade do executa formatos e analisa valores para exibição com base na cultura atual do usuário.</span><span class="sxs-lookup"><span data-stu-id="d70dd-114">Blazor's [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="d70dd-115">A cultura atual pode ser acessada a partir da <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> propriedade.</span><span class="sxs-lookup"><span data-stu-id="d70dd-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="d70dd-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> é usado para os seguintes tipos de campo ( `<input type="{TYPE}" />` ):</span><span class="sxs-lookup"><span data-stu-id="d70dd-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="d70dd-117">Os tipos de campo anteriores:</span><span class="sxs-lookup"><span data-stu-id="d70dd-117">The preceding field types:</span></span>

* <span data-ttu-id="d70dd-118">São exibidos usando suas regras de formatação baseadas em navegador apropriadas.</span><span class="sxs-lookup"><span data-stu-id="d70dd-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="d70dd-119">Não pode conter texto de forma livre.</span><span class="sxs-lookup"><span data-stu-id="d70dd-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="d70dd-120">Fornecer características de interação do usuário com base na implementação do navegador.</span><span class="sxs-lookup"><span data-stu-id="d70dd-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="d70dd-121">Os seguintes tipos de campo têm requisitos de formatação específicos e não têm suporte no momento Blazor porque não têm suporte de todos os principais navegadores:</span><span class="sxs-lookup"><span data-stu-id="d70dd-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="d70dd-122">[`@bind`](xref:mvc/views/razor#bind) dá suporte ao `@bind:culture` parâmetro para fornecer um <xref:System.Globalization.CultureInfo?displayProperty=fullName> para análise e formatação de um valor.</span><span class="sxs-lookup"><span data-stu-id="d70dd-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="d70dd-123">Não é recomendável especificar uma cultura ao usar os `date` `number` tipos de campo e.</span><span class="sxs-lookup"><span data-stu-id="d70dd-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="d70dd-124">`date` e `number` têm Blazor suporte interno que fornece a cultura necessária.</span><span class="sxs-lookup"><span data-stu-id="d70dd-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="d70dd-125">Localização</span><span class="sxs-lookup"><span data-stu-id="d70dd-125">Localization</span></span>

### Blazor WebAssembly

<span data-ttu-id="d70dd-126">Blazor WebAssembly os aplicativos definem a cultura usando a [preferência de idioma](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)do usuário.</span><span class="sxs-lookup"><span data-stu-id="d70dd-126">Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="d70dd-127">Para configurar explicitamente a cultura, defina <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> e <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> em `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="d70dd-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d70dd-128">Por padrão, Blazor WebAssembly o carrega os recursos mínimos de globalização necessários para exibir valores, como datas e moedas, na cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="d70dd-128">By default, Blazor WebAssembly carries minimal globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="d70dd-129">Os aplicativos que devem oferecer suporte à alteração dinâmica da cultura devem ser configurados `BlazorWebAssemblyLoadAllGlobalizationData` no arquivo do projeto:</span><span class="sxs-lookup"><span data-stu-id="d70dd-129">Applications that must support dynamically changing the culture should configure `BlazorWebAssemblyLoadAllGlobalizationData` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyLoadAllGlobalizationData>true</BlazorWebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

<span data-ttu-id="d70dd-130">Se o aplicativo não exigir localização, você poderá configurar o aplicativo para dar suporte à cultura invariável, que se baseia na `en-US` cultura:</span><span class="sxs-lookup"><span data-stu-id="d70dd-130">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="d70dd-131">Por padrão, a configuração do vinculador de IL (linguagem intermediária) para Blazor WebAssembly aplicativos retira informações de internacionalização, exceto as localidades explicitamente solicitadas.</span><span class="sxs-lookup"><span data-stu-id="d70dd-131">By default, the Intermediate Language (IL) Linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="d70dd-132">Para obter mais informações, consulte <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="d70dd-132">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="d70dd-133">Embora a cultura que Blazor seleciona por padrão possa ser suficiente para a maioria dos usuários, considere a possibilidade de oferecer uma maneira para os usuários especificarem sua localidade preferida.</span><span class="sxs-lookup"><span data-stu-id="d70dd-133">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="d70dd-134">Para um Blazor WebAssembly aplicativo de exemplo com um seletor de cultura, consulte o [`LocSample`](https://github.com/pranavkm/LocSample) aplicativo de exemplo de localização.</span><span class="sxs-lookup"><span data-stu-id="d70dd-134">For a Blazor WebAssembly sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### Blazor Server

<span data-ttu-id="d70dd-135">Blazor Server os aplicativos são localizados usando o [middleware de localização](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="d70dd-135">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="d70dd-136">O middleware seleciona a cultura apropriada para os usuários que solicitam recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d70dd-136">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="d70dd-137">A cultura pode ser definida usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="d70dd-137">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="d70dd-138">[Cookies](#cookies)</span><span class="sxs-lookup"><span data-stu-id="d70dd-138">[Cookies](#cookies)</span></span>
* [<span data-ttu-id="d70dd-139">Fornecer interface do usuário para escolher a cultura</span><span class="sxs-lookup"><span data-stu-id="d70dd-139">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="d70dd-140">Para obter mais informações e exemplos, consulte <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="d70dd-140">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="no-loccookies"></a><span data-ttu-id="d70dd-141">Cookies</span><span class="sxs-lookup"><span data-stu-id="d70dd-141">Cookies</span></span>

<span data-ttu-id="d70dd-142">Uma cultura de localização cookie pode persistir a cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="d70dd-142">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="d70dd-143">O middleware de localização lê o cookie em solicitações subsequentes para definir a cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="d70dd-143">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="d70dd-144">O uso de um cookie garante que a conexão WebSocket possa propagar corretamente a cultura.</span><span class="sxs-lookup"><span data-stu-id="d70dd-144">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="d70dd-145">Se os esquemas de localização forem baseados no caminho da URL ou na cadeia de caracteres de consulta, o esquema pode não ser capaz de trabalhar com WebSockets, portanto, falha ao persistir a cultura.</span><span class="sxs-lookup"><span data-stu-id="d70dd-145">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="d70dd-146">Portanto, o uso de uma cultura de localização cookie é a abordagem recomendada.</span><span class="sxs-lookup"><span data-stu-id="d70dd-146">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="d70dd-147">Qualquer técnica pode ser usada para atribuir uma cultura se a cultura for persistida em uma localização cookie .</span><span class="sxs-lookup"><span data-stu-id="d70dd-147">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="d70dd-148">Se o aplicativo já tiver um esquema de localização estabelecido para ASP.NET Core do lado do servidor, continue a usar a infraestrutura de localização existente do aplicativo e defina a cultura de localização cookie no esquema do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d70dd-148">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="d70dd-149">O exemplo a seguir mostra como definir a cultura atual em um cookie que pode ser lida pelo middleware de localização.</span><span class="sxs-lookup"><span data-stu-id="d70dd-149">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="d70dd-150">Crie uma Razor expressão no `Pages/_Host.cshtml` arquivo imediatamente dentro da marca de abertura `<body>` :</span><span class="sxs-lookup"><span data-stu-id="d70dd-150">Create a Razor expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="d70dd-151">A localização é manipulada pelo aplicativo na seguinte sequência de eventos:</span><span class="sxs-lookup"><span data-stu-id="d70dd-151">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="d70dd-152">O navegador envia uma solicitação HTTP inicial para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d70dd-152">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="d70dd-153">A cultura é atribuída pelo middleware de localização.</span><span class="sxs-lookup"><span data-stu-id="d70dd-153">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="d70dd-154">A Razor expressão na `_Host` página ( `_Host.cshtml` ) persiste a cultura em um cookie como parte da resposta.</span><span class="sxs-lookup"><span data-stu-id="d70dd-154">The Razor expression in the `_Host` page (`_Host.cshtml`) persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="d70dd-155">O navegador abre uma conexão WebSocket para criar uma Blazor Server sessão interativa.</span><span class="sxs-lookup"><span data-stu-id="d70dd-155">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="d70dd-156">O middleware de localização lê o cookie e atribui a cultura.</span><span class="sxs-lookup"><span data-stu-id="d70dd-156">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="d70dd-157">A Blazor Server sessão começa com a cultura correta.</span><span class="sxs-lookup"><span data-stu-id="d70dd-157">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="d70dd-158">Fornecer interface do usuário para escolher a cultura</span><span class="sxs-lookup"><span data-stu-id="d70dd-158">Provide UI to choose the culture</span></span>

<span data-ttu-id="d70dd-159">Para fornecer à interface do usuário a fim de permitir a seleção de uma cultura, é recomendável uma *abordagem baseada em redirecionamento* .</span><span class="sxs-lookup"><span data-stu-id="d70dd-159">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="d70dd-160">O processo é semelhante ao que acontece em um aplicativo Web quando um usuário tenta acessar um recurso seguro.</span><span class="sxs-lookup"><span data-stu-id="d70dd-160">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="d70dd-161">O usuário é redirecionado para uma página de entrada e Redirecionado de volta para o recurso original.</span><span class="sxs-lookup"><span data-stu-id="d70dd-161">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="d70dd-162">O aplicativo persiste a cultura selecionada do usuário por meio de um redirecionamento para um controlador.</span><span class="sxs-lookup"><span data-stu-id="d70dd-162">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="d70dd-163">O controlador define a cultura selecionada do usuário em um cookie e redireciona o usuário de volta para o URI original.</span><span class="sxs-lookup"><span data-stu-id="d70dd-163">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="d70dd-164">Estabeleça um ponto de extremidade HTTP no servidor para definir a cultura selecionada do usuário em um cookie e execute o redirecionamento de volta para o URI original:</span><span class="sxs-lookup"><span data-stu-id="d70dd-164">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="d70dd-165">Use o <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> resultado da ação para evitar ataques de redirecionamento abertos.</span><span class="sxs-lookup"><span data-stu-id="d70dd-165">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="d70dd-166">Para obter mais informações, consulte <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="d70dd-166">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="d70dd-167">Se o aplicativo não estiver configurado para processar ações do controlador:</span><span class="sxs-lookup"><span data-stu-id="d70dd-167">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="d70dd-168">Adicionar serviços MVC à coleção de serviços em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d70dd-168">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="d70dd-169">Adicionar roteamento de ponto de extremidade do controlador em `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d70dd-169">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="d70dd-170">O componente a seguir mostra um exemplo de como executar o redirecionamento inicial quando o usuário seleciona uma cultura:</span><span class="sxs-lookup"><span data-stu-id="d70dd-170">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="d70dd-171">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d70dd-171">Additional resources</span></span>

* <xref:fundamentals/localization>
