---
title: ASP.NET Core Blazor WebAssembly cenários de segurança adicionais
author: guardrex
description: Saiba como configurar o Blazor WebAssembly para cenários de segurança adicionais.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 15531c39a66a9f6dfd0f5c20cf960e4db5a78074
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013795"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="eca49-103">ASP.NET Core Blazor WebAssembly cenários de segurança adicionais</span><span class="sxs-lookup"><span data-stu-id="eca49-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="eca49-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eca49-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="eca49-105">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="eca49-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="eca49-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>é <xref:System.Net.Http.DelegatingHandler> usado para anexar tokens de acesso a instâncias de saída <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="eca49-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> is a <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="eca49-107">Os tokens são adquiridos usando o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> serviço, que é registrado pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="eca49-107">Tokens are acquired using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, which is registered by the framework.</span></span> <span data-ttu-id="eca49-108">Se um token não puder ser adquirido, um <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> será gerado.</span><span class="sxs-lookup"><span data-stu-id="eca49-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="eca49-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>tem um <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> método que pode ser usado para navegar pelo usuário para o provedor de identidade a fim de adquirir um novo token.</span><span class="sxs-lookup"><span data-stu-id="eca49-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span>

<span data-ttu-id="eca49-110">Para sua conveniência, a estrutura fornece o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> pré-configurado com o endereço base do aplicativo como uma URL autorizada.</span><span class="sxs-lookup"><span data-stu-id="eca49-110">For convenience, the framework provides the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="eca49-111">**Tokens de acesso são adicionados somente quando o URI de solicitação está dentro do URI base do aplicativo.**</span><span class="sxs-lookup"><span data-stu-id="eca49-111">**Access tokens are only added when the request URI is within the app's base URI.**</span></span> <span data-ttu-id="eca49-112">Quando os URIs de solicitação de saída não estiverem dentro do URI base do aplicativo, use uma [ `AuthorizationMessageHandler` classe personalizada (*recomendado*)](#custom-authorizationmessagehandler-class) ou [Configure o `AuthorizationMessageHandler` ](#configure-authorizationmessagehandler).</span><span class="sxs-lookup"><span data-stu-id="eca49-112">When outgoing request URIs aren't within the app's base URI, use a [custom `AuthorizationMessageHandler` class (*recommended*)](#custom-authorizationmessagehandler-class) or [configure the `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span></span>

> [!NOTE]
> <span data-ttu-id="eca49-113">Além da configuração do aplicativo cliente para acesso à API do servidor, a API do servidor também deve permitir solicitações entre origens (CORS) quando o cliente e o servidor não residem no mesmo endereço base.</span><span class="sxs-lookup"><span data-stu-id="eca49-113">In addition to the client app configuration for server API access, the server API must also allow cross-origin requests (CORS) when the client and the server don't reside at the same base address.</span></span> <span data-ttu-id="eca49-114">Para obter mais informações sobre a configuração CORS do lado do servidor, consulte a seção [CORS (compartilhamento de recursos entre origens)](#cross-origin-resource-sharing-cors) mais adiante neste artigo.</span><span class="sxs-lookup"><span data-stu-id="eca49-114">For more information on server-side CORS configuration, see the [Cross-origin resource sharing (CORS)](#cross-origin-resource-sharing-cors) section later in this article.</span></span>

<span data-ttu-id="eca49-115">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="eca49-115">In the following example:</span></span>

* <span data-ttu-id="eca49-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A>Adiciona <xref:System.Net.Http.IHttpClientFactory> e serviços relacionados à coleção de serviços e configura um nome <xref:System.Net.Http.HttpClient> ( `ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="eca49-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> adds <xref:System.Net.Http.IHttpClientFactory> and related services to the service collection and configures a named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span> <span data-ttu-id="eca49-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>é o endereço base do URI do recurso ao enviar solicitações.</span><span class="sxs-lookup"><span data-stu-id="eca49-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is the base address of the resource URI when sending requests.</span></span> <span data-ttu-id="eca49-118"><xref:System.Net.Http.IHttpClientFactory>é fornecido pelo [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="eca49-118"><xref:System.Net.Http.IHttpClientFactory> is provided by the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package.</span></span>
* <span data-ttu-id="eca49-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>é <xref:System.Net.Http.DelegatingHandler> usado para anexar tokens de acesso a instâncias de saída <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="eca49-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is the <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="eca49-120">Tokens de acesso são adicionados somente quando o URI de solicitação está dentro do URI base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eca49-120">Access tokens are only added when the request URI is within the app's base URI.</span></span>
* <span data-ttu-id="eca49-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType>Cria e configura uma <xref:System.Net.Http.HttpClient> instância para solicitações de saída usando a configuração que corresponde ao nome <xref:System.Net.Http.HttpClient> ( `ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="eca49-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> creates and configures an <xref:System.Net.Http.HttpClient> instance for outgoing requests using the configuration that corresponds to the named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="eca49-122">Para um Blazor aplicativo baseado no Blazor WebAssembly modelo de projeto hospedado, os URIs de solicitação estão dentro do URI base do aplicativo por padrão.</span><span class="sxs-lookup"><span data-stu-id="eca49-122">For a Blazor app based on the Blazor WebAssembly Hosted project template, request URIs are within the app's base URI by default.</span></span> <span data-ttu-id="eca49-123">Portanto, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) é atribuído ao <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> em um aplicativo gerado a partir do modelo de projeto.</span><span class="sxs-lookup"><span data-stu-id="eca49-123">Therefore, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in an app generated from the project template.</span></span>

<span data-ttu-id="eca49-124">O configurado <xref:System.Net.Http.HttpClient> é usado para fazer solicitações autorizadas usando o [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) padrão:</span><span class="sxs-lookup"><span data-stu-id="eca49-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="eca49-125">`AuthorizationMessageHandler`Classe personalizada</span><span class="sxs-lookup"><span data-stu-id="eca49-125">Custom `AuthorizationMessageHandler` class</span></span>

<span data-ttu-id="eca49-126">*Estas diretrizes nesta seção são recomendadas para aplicativos cliente que fazem solicitações de saída para URIs que não estão dentro do URI base do aplicativo.*</span><span class="sxs-lookup"><span data-stu-id="eca49-126">*This guidance in this section is recommended for client apps that make outgoing requests to URIs that aren't within the app's base URI.*</span></span>

<span data-ttu-id="eca49-127">No exemplo a seguir, uma classe personalizada se estende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> para uso como <xref:System.Net.Http.DelegatingHandler> para um <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="eca49-127">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> for use as the <xref:System.Net.Http.DelegatingHandler> for an <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="eca49-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>configura esse manipulador para autorizar solicitações HTTP de saída usando um token de acesso.</span><span class="sxs-lookup"><span data-stu-id="eca49-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures this handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="eca49-129">O token de acesso só será anexado se pelo menos uma das URLs autorizadas for uma base do URI de solicitação ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ).</span><span class="sxs-lookup"><span data-stu-id="eca49-129">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="eca49-130">No `Program.Main` ( `Program.cs` ), o `CustomAuthorizationMessageHandler` é registrado como um serviço com escopo e é configurado como o <xref:System.Net.Http.DelegatingHandler> para instâncias de saída <xref:System.Net.Http.HttpResponseMessage> feitas por um nome <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="eca49-130">In `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` is registered as a scoped service and is configured as the <xref:System.Net.Http.DelegatingHandler> for outgoing <xref:System.Net.Http.HttpResponseMessage> instances made by a named <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="eca49-131">Para um Blazor aplicativo baseado no Blazor WebAssembly modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) é atribuído ao <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> por padrão.</span><span class="sxs-lookup"><span data-stu-id="eca49-131">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="eca49-132">O configurado <xref:System.Net.Http.HttpClient> é usado para fazer solicitações autorizadas usando o [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) padrão.</span><span class="sxs-lookup"><span data-stu-id="eca49-132">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="eca49-133">Em que o cliente é criado com <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pacote), as <xref:System.Net.Http.HttpClient> instâncias do são fornecidas que incluem tokens de acesso ao fazer solicitações para a API do servidor.</span><span class="sxs-lookup"><span data-stu-id="eca49-133">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API.</span></span> <span data-ttu-id="eca49-134">Se o URI de solicitação for um URI relativo, como no exemplo a seguir ( `ExampleAPIMethod` ), ele será combinado com o <xref:System.Net.Http.HttpClient.BaseAddress> quando o aplicativo cliente fizer a solicitação:</span><span class="sxs-lookup"><span data-stu-id="eca49-134">If the request URI is a relative URI, as it is in the following example (`ExampleAPIMethod`), it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress> when the client app makes the request:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="eca49-135">configurar `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="eca49-135">Configure `AuthorizationMessageHandler`</span></span>

<span data-ttu-id="eca49-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>pode ser configurado com URLs autorizadas, escopos e uma URL de retorno usando o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> método.</span><span class="sxs-lookup"><span data-stu-id="eca49-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with authorized URLs, scopes, and a return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span> <span data-ttu-id="eca49-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>configura o manipulador para autorizar solicitações HTTP de saída usando um token de acesso.</span><span class="sxs-lookup"><span data-stu-id="eca49-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures the handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="eca49-138">O token de acesso só será anexado se pelo menos uma das URLs autorizadas for uma base do URI de solicitação ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ).</span><span class="sxs-lookup"><span data-stu-id="eca49-138">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span> <span data-ttu-id="eca49-139">Se o URI de solicitação for um URI relativo, ele será combinado com o <xref:System.Net.Http.HttpClient.BaseAddress> .</span><span class="sxs-lookup"><span data-stu-id="eca49-139">If the request URI is a relative URI, it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress>.</span></span>

<span data-ttu-id="eca49-140">No exemplo a seguir, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configura um <xref:System.Net.Http.HttpClient> in `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="eca49-140">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

<span data-ttu-id="eca49-141">Para um Blazor aplicativo baseado no Blazor WebAssembly modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> é atribuído ao seguinte por padrão:</span><span class="sxs-lookup"><span data-stu-id="eca49-141">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="eca49-142">O <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="eca49-142">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="eca49-143">Uma URL da `authorizedUrls` matriz.</span><span class="sxs-lookup"><span data-stu-id="eca49-143">A URL of the `authorizedUrls` array.</span></span>

## <a name="typed-httpclient"></a><span data-ttu-id="eca49-144">Automaticamente`HttpClient`</span><span class="sxs-lookup"><span data-stu-id="eca49-144">Typed `HttpClient`</span></span>

<span data-ttu-id="eca49-145">Um cliente tipado pode ser definido para lidar com todas as preocupações de aquisição de token e HTTP em uma única classe.</span><span class="sxs-lookup"><span data-stu-id="eca49-145">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="eca49-146">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="eca49-146">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="eca49-147">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `using static BlazorSample.Data;` ).</span><span class="sxs-lookup"><span data-stu-id="eca49-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="eca49-148">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="eca49-148">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="eca49-149">Para um Blazor aplicativo baseado no Blazor WebAssembly modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) é atribuído ao <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> por padrão.</span><span class="sxs-lookup"><span data-stu-id="eca49-149">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="eca49-150">`FetchData`componente ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="eca49-150">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="eca49-151">Configurar o `HttpClient` manipulador</span><span class="sxs-lookup"><span data-stu-id="eca49-151">Configure the `HttpClient` handler</span></span>

<span data-ttu-id="eca49-152">O manipulador pode ser configurado ainda mais com <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="eca49-152">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="eca49-153">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="eca49-153">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="eca49-154">Para um Blazor aplicativo baseado no Blazor WebAssembly modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> é atribuído ao seguinte por padrão:</span><span class="sxs-lookup"><span data-stu-id="eca49-154">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="eca49-155">O <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="eca49-155">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="eca49-156">Uma URL da `authorizedUrls` matriz.</span><span class="sxs-lookup"><span data-stu-id="eca49-156">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="eca49-157">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="eca49-157">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="eca49-158">Se o Blazor WebAssembly aplicativo normalmente usa um padrão seguro <xref:System.Net.Http.HttpClient> , o aplicativo também pode fazer solicitações de API da Web não autenticadas ou não autorizadas Configurando um nome <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="eca49-158">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="eca49-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="eca49-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="eca49-160">Para um Blazor aplicativo baseado no Blazor WebAssembly modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) é atribuído ao <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> por padrão.</span><span class="sxs-lookup"><span data-stu-id="eca49-160">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="eca49-161">O registro anterior é além do registro padrão seguro existente <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="eca49-161">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="eca49-162">Um componente cria o <xref:System.Net.Http.HttpClient> do <xref:System.Net.Http.IHttpClientFactory> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pacote) para fazer solicitações não autenticadas ou não autorizadas:</span><span class="sxs-lookup"><span data-stu-id="eca49-162">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="eca49-163">O controlador na API do servidor, `WeatherForecastNoAuthenticationController` para o exemplo anterior, não está marcado com o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="eca49-163">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="eca49-164">A decisão de usar um cliente seguro ou um cliente inseguro, pois a <xref:System.Net.Http.HttpClient> instância padrão é até o desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="eca49-164">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="eca49-165">Uma maneira de tomar essa decisão é considerar o número de pontos de extremidade autenticados versus não autenticados que o aplicativo contata.</span><span class="sxs-lookup"><span data-stu-id="eca49-165">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="eca49-166">Se a maioria das solicitações do aplicativo for proteger pontos de extremidade de API, use a instância autenticada <xref:System.Net.Http.HttpClient> como o padrão.</span><span class="sxs-lookup"><span data-stu-id="eca49-166">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="eca49-167">Caso contrário, registre a instância não autenticada <xref:System.Net.Http.HttpClient> como o padrão.</span><span class="sxs-lookup"><span data-stu-id="eca49-167">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="eca49-168">Uma abordagem alternativa para usar o <xref:System.Net.Http.IHttpClientFactory> é criar um [cliente tipado](#typed-httpclient) para acesso não autenticado a pontos de extremidade anônimos.</span><span class="sxs-lookup"><span data-stu-id="eca49-168">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="eca49-169">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="eca49-169">Request additional access tokens</span></span>

<span data-ttu-id="eca49-170">Os tokens de acesso podem ser obtidos manualmente chamando `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="eca49-170">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="eca49-171">No exemplo a seguir, Azure Active Directory adicionais (AAD) Microsoft Graph escopos de API são exigidos por um aplicativo para ler dados do usuário e enviar email.</span><span class="sxs-lookup"><span data-stu-id="eca49-171">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="eca49-172">Depois de adicionar as permissões de API do Microsoft Graph no portal do Azure AAD, os escopos adicionais são configurados no aplicativo cliente.</span><span class="sxs-lookup"><span data-stu-id="eca49-172">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="eca49-173">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="eca49-173">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

<span data-ttu-id="eca49-174">O `IAccessTokenProvider.RequestToken` método fornece uma sobrecarga que permite que um aplicativo provisione um token de acesso com um determinado conjunto de escopos.</span><span class="sxs-lookup"><span data-stu-id="eca49-174">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="eca49-175">Em um Razor componente:</span><span class="sxs-lookup"><span data-stu-id="eca49-175">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="eca49-176"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>apresenta</span><span class="sxs-lookup"><span data-stu-id="eca49-176"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="eca49-177">`true`com o `token` para uso.</span><span class="sxs-lookup"><span data-stu-id="eca49-177">`true` with the `token` for use.</span></span>
* <span data-ttu-id="eca49-178">`false`Se o token não for recuperado.</span><span class="sxs-lookup"><span data-stu-id="eca49-178">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="eca49-179">`HttpClient`e `HttpRequestMessage` com opções de solicitação de API de busca</span><span class="sxs-lookup"><span data-stu-id="eca49-179">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>

<span data-ttu-id="eca49-180">Ao executar em Webassembly em um Blazor WebAssembly aplicativo, [`HttpClient`](xref:fundamentals/http-requests) ([documentação da API](xref:System.Net.Http.HttpClient)) e <xref:System.Net.Http.HttpRequestMessage> pode ser usado para personalizar solicitações.</span><span class="sxs-lookup"><span data-stu-id="eca49-180">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="eca49-181">Por exemplo, você pode especificar o método HTTP e os cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="eca49-181">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="eca49-182">O componente a seguir faz uma `POST` solicitação para um ponto de extremidade de API de lista de tarefas no servidor e mostra o corpo da resposta:</span><span class="sxs-lookup"><span data-stu-id="eca49-182">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="eca49-183">A implementação do .NET Webassembly de <xref:System.Net.Http.HttpClient> usa [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="eca49-183">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="eca49-184">FETCH permite configurar várias [opções específicas de solicitação](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="eca49-184">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="eca49-185">As opções de solicitação de busca HTTP podem ser configuradas com <xref:System.Net.Http.HttpRequestMessage> métodos de extensão mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="eca49-185">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="eca49-186">Método de extensão</span><span class="sxs-lookup"><span data-stu-id="eca49-186">Extension method</span></span> | <span data-ttu-id="eca49-187">Buscar propriedade de solicitação</span><span class="sxs-lookup"><span data-stu-id="eca49-187">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="eca49-188">Você pode definir opções adicionais usando o método de <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extensão mais genérico.</span><span class="sxs-lookup"><span data-stu-id="eca49-188">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="eca49-189">A resposta HTTP é normalmente armazenada em buffer em um Blazor WebAssembly aplicativo para habilitar o suporte para leituras de sincronização no conteúdo da resposta.</span><span class="sxs-lookup"><span data-stu-id="eca49-189">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="eca49-190">Para habilitar o suporte para streaming de resposta, use o <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> método de extensão na solicitação.</span><span class="sxs-lookup"><span data-stu-id="eca49-190">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="eca49-191">Para incluir credenciais em uma solicitação entre origens, use o <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> método de extensão:</span><span class="sxs-lookup"><span data-stu-id="eca49-191">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="eca49-192">Para obter mais informações sobre as opções de API de busca, consulte [MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="eca49-192">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="eca49-193">CORS (compartilhamento de recursos entre origens)</span><span class="sxs-lookup"><span data-stu-id="eca49-193">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="eca49-194">Ao enviar credenciais ( cookie s/cabeçalhos de autorização) em solicitações CORS, o `Authorization` cabeçalho deve ser permitido pela política CORS.</span><span class="sxs-lookup"><span data-stu-id="eca49-194">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="eca49-195">A política a seguir inclui a configuração para o:</span><span class="sxs-lookup"><span data-stu-id="eca49-195">The following policy includes configuration for:</span></span>

* <span data-ttu-id="eca49-196">Origens da solicitação ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="eca49-196">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="eca49-197">Qualquer método (verbo).</span><span class="sxs-lookup"><span data-stu-id="eca49-197">Any method (verb).</span></span>
* <span data-ttu-id="eca49-198">`Content-Type``Authorization`cabeçalhos e.</span><span class="sxs-lookup"><span data-stu-id="eca49-198">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="eca49-199">Para permitir um cabeçalho personalizado (por exemplo, `x-custom-header` ), liste o cabeçalho ao chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="eca49-199">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="eca49-200">Credenciais definidas pelo código JavaScript do lado do cliente ( `credentials` propriedade definida como `include` ).</span><span class="sxs-lookup"><span data-stu-id="eca49-200">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="eca49-201">Uma solução hospedada Blazor com base no Blazor modelo de projeto hospedado usa o mesmo endereço base para os aplicativos cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="eca49-201">A hosted Blazor solution based on the Blazor Hosted project template uses the same base address for the client and server apps.</span></span> <span data-ttu-id="eca49-202">O aplicativo cliente <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> é definido como um URI de `builder.HostEnvironment.BaseAddress` por padrão.</span><span class="sxs-lookup"><span data-stu-id="eca49-202">The client app's <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is set to a URI of `builder.HostEnvironment.BaseAddress` by default.</span></span> <span data-ttu-id="eca49-203">A configuração de CORS **não** é necessária na configuração padrão de um aplicativo hospedado criado a partir do Blazor modelo de projeto hospedado.</span><span class="sxs-lookup"><span data-stu-id="eca49-203">CORS configuration is **not** required in the default configuration of a hosted app created from the Blazor Hosted project template.</span></span> <span data-ttu-id="eca49-204">Aplicativos cliente adicionais que não são hospedados pelo projeto do servidor e que não compartilham **o endereço base do aplicativo do servidor** exigem a configuração CORS no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="eca49-204">Additional client apps that aren't hosted by the server project and don't share the server app's base address **do** require CORS configuration in the server project.</span></span>

<span data-ttu-id="eca49-205">Para obter mais informações, consulte <xref:security/cors> e o componente testador de solicitação HTTP do aplicativo de exemplo ( `Components/HTTPRequestTester.razor` ).</span><span class="sxs-lookup"><span data-stu-id="eca49-205">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="eca49-206">Manipular erros de solicitação de token</span><span class="sxs-lookup"><span data-stu-id="eca49-206">Handle token request errors</span></span>

<span data-ttu-id="eca49-207">Quando um aplicativo de página única (SPA) autentica um usuário usando o OpenID Connect (OIDC), o estado de autenticação é mantido localmente no SPA e no Identity provedor (IP) na forma de uma sessão cookie definida como resultado do usuário que fornece suas credenciais.</span><span class="sxs-lookup"><span data-stu-id="eca49-207">When a Single Page Application (SPA) authenticates a user using OpenID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="eca49-208">Os tokens emitidos pelo IP para o usuário normalmente são válidos por curto período de tempo, cerca de uma hora normalmente, de modo que o aplicativo cliente deve buscar regularmente novos tokens.</span><span class="sxs-lookup"><span data-stu-id="eca49-208">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="eca49-209">Caso contrário, o usuário será desconectado após os tokens concedidos expirarem.</span><span class="sxs-lookup"><span data-stu-id="eca49-209">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="eca49-210">Na maioria dos casos, os clientes do OIDC são capazes de provisionar novos tokens sem exigir que o usuário se autentique novamente graças ao estado de autenticação ou "sessão" que é mantido dentro do IP.</span><span class="sxs-lookup"><span data-stu-id="eca49-210">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="eca49-211">Há alguns casos em que o cliente não pode obter um token sem interação do usuário, por exemplo, quando por algum motivo o usuário faz logoff explicitamente do IP.</span><span class="sxs-lookup"><span data-stu-id="eca49-211">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="eca49-212">Esse cenário ocorre se um usuário visita `https://login.microsoftonline.com` e faz logoff. Nesses cenários, o aplicativo não sabe imediatamente que o usuário fez logoff. Qualquer token que o cliente contém pode não ser mais válido.</span><span class="sxs-lookup"><span data-stu-id="eca49-212">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="eca49-213">Além disso, o cliente não é capaz de provisionar um novo token sem interação do usuário depois que o token atual expira.</span><span class="sxs-lookup"><span data-stu-id="eca49-213">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="eca49-214">Esses cenários não são específicos para a autenticação baseada em token.</span><span class="sxs-lookup"><span data-stu-id="eca49-214">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="eca49-215">Eles fazem parte da natureza do SPAs.</span><span class="sxs-lookup"><span data-stu-id="eca49-215">They are part of the nature of SPAs.</span></span> <span data-ttu-id="eca49-216">Um SPA usando cookie s também falha ao chamar uma API de servidor se a autenticação cookie for removida.</span><span class="sxs-lookup"><span data-stu-id="eca49-216">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="eca49-217">Quando um aplicativo executa chamadas à API para recursos protegidos, você deve estar atento ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="eca49-217">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="eca49-218">Para provisionar um novo token de acesso para chamar a API, o usuário pode ser solicitado a autenticar novamente.</span><span class="sxs-lookup"><span data-stu-id="eca49-218">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="eca49-219">Mesmo que o cliente tenha um token que pareça ser válido, a chamada para o servidor pode falhar porque o token foi revogado pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="eca49-219">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="eca49-220">Quando o aplicativo solicita um token, há dois resultados possíveis:</span><span class="sxs-lookup"><span data-stu-id="eca49-220">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="eca49-221">A solicitação é realizada com sucesso e o aplicativo tem um token válido.</span><span class="sxs-lookup"><span data-stu-id="eca49-221">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="eca49-222">A solicitação falha e o aplicativo deve autenticar o usuário novamente para obter um novo token.</span><span class="sxs-lookup"><span data-stu-id="eca49-222">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="eca49-223">Quando uma solicitação de token falha, você precisa decidir se deseja salvar qualquer estado atual antes de executar um redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="eca49-223">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="eca49-224">Existem várias abordagens com níveis crescentes de complexidade:</span><span class="sxs-lookup"><span data-stu-id="eca49-224">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="eca49-225">Armazene o estado da página atual no armazenamento de sessão.</span><span class="sxs-lookup"><span data-stu-id="eca49-225">Store the current page state in session storage.</span></span> <span data-ttu-id="eca49-226">Durante o [ `OnInitializedAsync` evento de ciclo de vida](xref:blazor/components/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), verifique se o estado pode ser restaurado antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="eca49-226">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="eca49-227">Adicione um parâmetro de cadeia de caracteres de consulta e use-o como uma maneira de sinalizar ao aplicativo que ele precisa Rehidratar o estado salvo anteriormente.</span><span class="sxs-lookup"><span data-stu-id="eca49-227">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="eca49-228">Adicione um parâmetro de cadeia de caracteres de consulta com um identificador exclusivo para armazenar dados no armazenamento de sessão sem risco de colisões com outros itens.</span><span class="sxs-lookup"><span data-stu-id="eca49-228">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="eca49-229">O exemplo a seguir mostra como:</span><span class="sxs-lookup"><span data-stu-id="eca49-229">The following example shows how to:</span></span>

* <span data-ttu-id="eca49-230">Preserve o estado antes de redirecionar para a página de logon.</span><span class="sxs-lookup"><span data-stu-id="eca49-230">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="eca49-231">Recupere o estado anterior depois da autenticação usando o parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="eca49-231">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="eca49-232">Salvar o estado do aplicativo antes de uma operação de autenticação</span><span class="sxs-lookup"><span data-stu-id="eca49-232">Save app state before an authentication operation</span></span>

<span data-ttu-id="eca49-233">Durante uma operação de autenticação, há casos em que você deseja salvar o estado do aplicativo antes que o navegador seja redirecionado para o IP.</span><span class="sxs-lookup"><span data-stu-id="eca49-233">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="eca49-234">Esse pode ser o caso quando você estiver usando um contêiner de estado e quiser restaurar o estado depois que a autenticação for realizada com sucesso.</span><span class="sxs-lookup"><span data-stu-id="eca49-234">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="eca49-235">Você pode usar um objeto de estado de autenticação personalizado para preservar o estado específico do aplicativo ou uma referência a ele e restaurar esse estado depois que a operação de autenticação for concluída com êxito.</span><span class="sxs-lookup"><span data-stu-id="eca49-235">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="eca49-236">O exemplo a seguir demonstra a abordagem.</span><span class="sxs-lookup"><span data-stu-id="eca49-236">The following example demonstrates the approach.</span></span>

<span data-ttu-id="eca49-237">Uma classe de contêiner de estado é criada no aplicativo com propriedades para manter os valores de estado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eca49-237">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="eca49-238">No exemplo a seguir, o contêiner é usado para manter o valor do contador do componente padrão do modelo de projeto `Counter` ( `Pages/Counter.razor` ).</span><span class="sxs-lookup"><span data-stu-id="eca49-238">In the following example, the container is used to maintain the counter value of the default project template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="eca49-239">Os métodos para serialização e desserialização do contêiner se baseiam em <xref:System.Text.Json> .</span><span class="sxs-lookup"><span data-stu-id="eca49-239">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="eca49-240">O `Counter` componente usa o contêiner de estado para manter o `currentCount` valor fora do componente:</span><span class="sxs-lookup"><span data-stu-id="eca49-240">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="eca49-241">Crie um `ApplicationAuthenticationState` de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> .</span><span class="sxs-lookup"><span data-stu-id="eca49-241">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="eca49-242">Forneça uma `Id` propriedade, que serve como um identificador para o estado armazenado localmente.</span><span class="sxs-lookup"><span data-stu-id="eca49-242">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="eca49-243">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="eca49-243">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="eca49-244">O `Authentication` componente ( `Pages/Authentication.razor` ) salva e restaura o estado do aplicativo usando o armazenamento de sessão local com os `StateContainer` métodos de serialização e desserialização, `GetStateForLocalStorage` e `SetStateFromLocalStorage` :</span><span class="sxs-lookup"><span data-stu-id="eca49-244">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="eca49-245">Este exemplo usa Azure Active Directory (AAD) para autenticação.</span><span class="sxs-lookup"><span data-stu-id="eca49-245">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="eca49-246">Em `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="eca49-246">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="eca49-247">O `ApplicationAuthenticationState` é configurado como o tipo de MSAL (biblioteca do Microsoft autenticação) `RemoteAuthenticationState` .</span><span class="sxs-lookup"><span data-stu-id="eca49-247">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="eca49-248">O contêiner de estado é registrado no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="eca49-248">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="eca49-249">Personalizar rotas de aplicativo</span><span class="sxs-lookup"><span data-stu-id="eca49-249">Customize app routes</span></span>

<span data-ttu-id="eca49-250">Por padrão, a [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) biblioteca usa as rotas mostradas na tabela a seguir para representar Estados de autenticação diferentes.</span><span class="sxs-lookup"><span data-stu-id="eca49-250">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="eca49-251">Rota</span><span class="sxs-lookup"><span data-stu-id="eca49-251">Route</span></span>                            | <span data-ttu-id="eca49-252">Finalidade</span><span class="sxs-lookup"><span data-stu-id="eca49-252">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="eca49-253">Dispara uma operação de entrada.</span><span class="sxs-lookup"><span data-stu-id="eca49-253">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="eca49-254">Manipula o resultado de qualquer operação de entrada.</span><span class="sxs-lookup"><span data-stu-id="eca49-254">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="eca49-255">Exibe mensagens de erro quando a operação de entrada falha por algum motivo.</span><span class="sxs-lookup"><span data-stu-id="eca49-255">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="eca49-256">Dispara uma operação de saída.</span><span class="sxs-lookup"><span data-stu-id="eca49-256">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="eca49-257">Lida com o resultado de uma operação de saída.</span><span class="sxs-lookup"><span data-stu-id="eca49-257">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="eca49-258">Exibe mensagens de erro quando a operação de saída falha por algum motivo.</span><span class="sxs-lookup"><span data-stu-id="eca49-258">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="eca49-259">Indica que o usuário fez logoff com êxito.</span><span class="sxs-lookup"><span data-stu-id="eca49-259">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="eca49-260">Dispara uma operação para editar o perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="eca49-260">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="eca49-261">Dispara uma operação para registrar um novo usuário.</span><span class="sxs-lookup"><span data-stu-id="eca49-261">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="eca49-262">As rotas mostradas na tabela anterior são configuráveis via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="eca49-262">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="eca49-263">Ao definir opções para fornecer rotas personalizadas, confirme se o aplicativo tem uma rota que manipula cada caminho.</span><span class="sxs-lookup"><span data-stu-id="eca49-263">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="eca49-264">No exemplo a seguir, todos os caminhos são prefixados com `/security` .</span><span class="sxs-lookup"><span data-stu-id="eca49-264">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="eca49-265">`Authentication`componente ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="eca49-265">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="eca49-266">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="eca49-266">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="eca49-267">Se o requisito chamar caminhos completamente diferentes, defina as rotas conforme descrito anteriormente e processe o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> com um parâmetro de ação explícita:</span><span class="sxs-lookup"><span data-stu-id="eca49-267">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="eca49-268">Você tem permissão para dividir a interface do usuário em páginas diferentes se optar por fazer isso.</span><span class="sxs-lookup"><span data-stu-id="eca49-268">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="eca49-269">Personalizar a interface do usuário de autenticação</span><span class="sxs-lookup"><span data-stu-id="eca49-269">Customize the authentication user interface</span></span>

<span data-ttu-id="eca49-270"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>inclui um conjunto padrão de partes da interface do usuário para cada Estado de autenticação.</span><span class="sxs-lookup"><span data-stu-id="eca49-270"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="eca49-271">Cada Estado pode ser personalizado passando um personalizado <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="eca49-271">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="eca49-272">Para personalizar o texto exibido durante o processo de logon inicial, o pode alterar o da <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="eca49-272">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="eca49-273">`Authentication`componente ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="eca49-273">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="eca49-274">O <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> tem um fragmento que pode ser usado por rota de autenticação mostrada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="eca49-274">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="eca49-275">Rota</span><span class="sxs-lookup"><span data-stu-id="eca49-275">Route</span></span>                            | <span data-ttu-id="eca49-276">Fragmento</span><span class="sxs-lookup"><span data-stu-id="eca49-276">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a><span data-ttu-id="eca49-277">Personalizar o usuário</span><span class="sxs-lookup"><span data-stu-id="eca49-277">Customize the user</span></span>

<span data-ttu-id="eca49-278">Os usuários associados ao aplicativo podem ser personalizados.</span><span class="sxs-lookup"><span data-stu-id="eca49-278">Users bound to the app can be customized.</span></span> <span data-ttu-id="eca49-279">No exemplo a seguir, todos os usuários autenticados recebem uma `amr` declaração para cada um dos métodos de autenticação do usuário.</span><span class="sxs-lookup"><span data-stu-id="eca49-279">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="eca49-280">Crie uma classe que estenda a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> classe:</span><span class="sxs-lookup"><span data-stu-id="eca49-280">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="eca49-281">Crie uma fábrica que estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="eca49-281">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

<span data-ttu-id="eca49-282">Registre o `CustomAccountFactory` para o provedor de autenticação em uso.</span><span class="sxs-lookup"><span data-stu-id="eca49-282">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="eca49-283">Qualquer um dos seguintes registros é válido:</span><span class="sxs-lookup"><span data-stu-id="eca49-283">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="eca49-284"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="eca49-284"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="eca49-285"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="eca49-285"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="eca49-286"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="eca49-286"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="eca49-287">Suporte ao pré-processamento com autenticação</span><span class="sxs-lookup"><span data-stu-id="eca49-287">Support prerendering with authentication</span></span>

<span data-ttu-id="eca49-288">Depois de seguir as diretrizes em um dos Blazor WebAssembly Tópicos do aplicativo hospedado, use as instruções a seguir para criar um aplicativo que:</span><span class="sxs-lookup"><span data-stu-id="eca49-288">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="eca49-289">Processa caminhos para os quais a autorização não é necessária.</span><span class="sxs-lookup"><span data-stu-id="eca49-289">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="eca49-290">Não PreRender caminhos para os quais a autorização é necessária.</span><span class="sxs-lookup"><span data-stu-id="eca49-290">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="eca49-291">Na classe do aplicativo cliente `Program` ( `Program.cs` ), fatorar registros de serviço comuns em um método separado (por exemplo, `ConfigureCommonServices` ):</span><span class="sxs-lookup"><span data-stu-id="eca49-291">In the Client app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="eca49-292">No aplicativo do servidor `Startup.ConfigureServices` , registre os seguintes serviços adicionais:</span><span class="sxs-lookup"><span data-stu-id="eca49-292">In the server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="eca49-293">No método do aplicativo do servidor `Startup.Configure` , substitua [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) por [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) :</span><span class="sxs-lookup"><span data-stu-id="eca49-293">In the server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="eca49-294">No aplicativo de servidor, crie uma `Pages` pasta se ela não existir.</span><span class="sxs-lookup"><span data-stu-id="eca49-294">In the server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="eca49-295">Crie uma `_Host.cshtml` página dentro da pasta do aplicativo do servidor `Pages` .</span><span class="sxs-lookup"><span data-stu-id="eca49-295">Create a `_Host.cshtml` page inside the server app's `Pages` folder.</span></span> <span data-ttu-id="eca49-296">Cole o conteúdo do arquivo do aplicativo cliente `wwwroot/index.html` no `Pages/_Host.cshtml` arquivo.</span><span class="sxs-lookup"><span data-stu-id="eca49-296">Paste the contents from the Client app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="eca49-297">Atualize o conteúdo do arquivo:</span><span class="sxs-lookup"><span data-stu-id="eca49-297">Update the file's contents:</span></span>

* <span data-ttu-id="eca49-298">Adicione `@page "_Host"` ao topo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="eca49-298">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="eca49-299">Substitua a `<app>Loading...</app>` marca pela seguinte:</span><span class="sxs-lookup"><span data-stu-id="eca49-299">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="eca49-300">Opções para aplicativos hospedados e provedores de logon de terceiros</span><span class="sxs-lookup"><span data-stu-id="eca49-300">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="eca49-301">Ao autenticar e autorizar um Blazor WebAssembly aplicativo hospedado com um provedor de terceiros, há várias opções disponíveis para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="eca49-301">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="eca49-302">O que você escolher dependerá de seu cenário.</span><span class="sxs-lookup"><span data-stu-id="eca49-302">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="eca49-303">Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="eca49-303">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="eca49-304">Autenticar usuários para chamar somente APIs de terceiros protegidas</span><span class="sxs-lookup"><span data-stu-id="eca49-304">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="eca49-305">Autentique o usuário com um fluxo OAuth do lado do cliente em relação ao provedor de API de terceiros:</span><span class="sxs-lookup"><span data-stu-id="eca49-305">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="eca49-306">Neste cenário:</span><span class="sxs-lookup"><span data-stu-id="eca49-306">In this scenario:</span></span>

* <span data-ttu-id="eca49-307">O servidor que hospeda o aplicativo não desempenha uma função.</span><span class="sxs-lookup"><span data-stu-id="eca49-307">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="eca49-308">As APIs no servidor não podem ser protegidas.</span><span class="sxs-lookup"><span data-stu-id="eca49-308">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="eca49-309">O aplicativo só pode chamar APIs de terceiros protegidas.</span><span class="sxs-lookup"><span data-stu-id="eca49-309">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="eca49-310">Autenticar usuários com um provedor de terceiros e chamar APIs protegidas no servidor host e terceiros</span><span class="sxs-lookup"><span data-stu-id="eca49-310">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="eca49-311">Configure Identity com um provedor de logon de terceiros.</span><span class="sxs-lookup"><span data-stu-id="eca49-311">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="eca49-312">Obtenha os tokens necessários para acesso à API de terceiros e armazene-os.</span><span class="sxs-lookup"><span data-stu-id="eca49-312">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="eca49-313">Quando um usuário faz logon, o Identity coleta tokens de acesso e de atualização como parte do processo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="eca49-313">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="eca49-314">Nesse ponto, há algumas abordagens disponíveis para fazer chamadas à API para APIs de terceiros.</span><span class="sxs-lookup"><span data-stu-id="eca49-314">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="eca49-315">Usar um token de acesso do servidor para recuperar o token de acesso de terceiros</span><span class="sxs-lookup"><span data-stu-id="eca49-315">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="eca49-316">Use o token de acesso gerado no servidor para recuperar o token de acesso de terceiros de um ponto de extremidade de API de servidor.</span><span class="sxs-lookup"><span data-stu-id="eca49-316">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="eca49-317">A partir daí, use o token de acesso de terceiros para chamar recursos de API de terceiros diretamente do Identity no cliente.</span><span class="sxs-lookup"><span data-stu-id="eca49-317">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="eca49-318">Não recomendamos essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="eca49-318">We don't recommend this approach.</span></span> <span data-ttu-id="eca49-319">Essa abordagem requer tratar o token de acesso de terceiros como se ele fosse gerado para um cliente público.</span><span class="sxs-lookup"><span data-stu-id="eca49-319">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="eca49-320">Nos termos do OAuth, o aplicativo público não tem um segredo do cliente porque não pode ser confiável para armazenar segredos com segurança e o token de acesso é produzido para um cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="eca49-320">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="eca49-321">Um cliente confidencial é um cliente que tem um segredo do cliente e é considerado capaz de armazenar segredos com segurança.</span><span class="sxs-lookup"><span data-stu-id="eca49-321">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="eca49-322">O token de acesso de terceiros pode receber escopos adicionais para executar operações confidenciais com base no fato de que a terceira parte emitiu o token para um cliente mais confiável.</span><span class="sxs-lookup"><span data-stu-id="eca49-322">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="eca49-323">Da mesma forma, os tokens de atualização não devem ser emitidos para um cliente que não seja confiável, pois isso dá ao cliente acesso ilimitado, a menos que outras restrições sejam colocadas em vigor.</span><span class="sxs-lookup"><span data-stu-id="eca49-323">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="eca49-324">Fazer chamadas à API do cliente para a API do servidor para chamar APIs de terceiros</span><span class="sxs-lookup"><span data-stu-id="eca49-324">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="eca49-325">Faça uma chamada de API do cliente para a API do servidor.</span><span class="sxs-lookup"><span data-stu-id="eca49-325">Make an API call from the client to the server API.</span></span> <span data-ttu-id="eca49-326">No servidor, recupere o token de acesso para o recurso de API de terceiros e emita qualquer chamada necessária.</span><span class="sxs-lookup"><span data-stu-id="eca49-326">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="eca49-327">Embora essa abordagem exija um salto de rede extra por meio do servidor para chamar uma API de terceiros, ela finalmente resulta em uma experiência mais segura:</span><span class="sxs-lookup"><span data-stu-id="eca49-327">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="eca49-328">O servidor pode armazenar tokens de atualização e garantir que o aplicativo não perca o acesso a recursos de terceiros.</span><span class="sxs-lookup"><span data-stu-id="eca49-328">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="eca49-329">O aplicativo não pode vazar tokens de acesso do servidor que possa conter permissões mais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="eca49-329">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="eca49-330">Usar pontos de extremidade do OpenID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="eca49-330">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="eca49-331">A biblioteca de autenticação e os Blazor modelos de projeto usam pontos de extremidade do OpenID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="eca49-331">The authentication library and Blazor project templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="eca49-332">Para usar um ponto de extremidade v 2.0, configure a opção de portador JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="eca49-332">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="eca49-333">No exemplo a seguir, o AAD está configurado para v 2.0 acrescentando um `v2.0` segmento à <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> Propriedade:</span><span class="sxs-lookup"><span data-stu-id="eca49-333">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="eca49-334">Como alternativa, a configuração pode ser feita no arquivo de configurações do aplicativo ( `appsettings.json` ):</span><span class="sxs-lookup"><span data-stu-id="eca49-334">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="eca49-335">Se a passagem de um segmento para a autoridade não for apropriada para o provedor de OIDC do aplicativo, como com provedores não AAD, defina a <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propriedade diretamente.</span><span class="sxs-lookup"><span data-stu-id="eca49-335">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="eca49-336">Defina a propriedade no <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> ou no arquivo de configurações do aplicativo ( `appsettings.json` ) com a `Authority` chave.</span><span class="sxs-lookup"><span data-stu-id="eca49-336">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="eca49-337">A lista de declarações no token de ID é alterada para pontos de extremidade v 2.0.</span><span class="sxs-lookup"><span data-stu-id="eca49-337">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="eca49-338">Para obter mais informações, consulte [por que atualizar para a plataforma Microsoft Identity (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="eca49-338">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="eca49-339">Configurar e usar o gRPC em componentes</span><span class="sxs-lookup"><span data-stu-id="eca49-339">Configure and use gRPC in components</span></span>

<span data-ttu-id="eca49-340">Para configurar um Blazor WebAssembly aplicativo para usar a [estrutura ASP.NET Core gRPC](xref:grpc/index):</span><span class="sxs-lookup"><span data-stu-id="eca49-340">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="eca49-341">Habilite o gRPC-Web no servidor.</span><span class="sxs-lookup"><span data-stu-id="eca49-341">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="eca49-342">Para obter mais informações, consulte <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="eca49-342">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="eca49-343">Registre os serviços gRPC para o manipulador de mensagens do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="eca49-343">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="eca49-344">O exemplo a seguir configura o manipulador de mensagens de autorização do aplicativo para usar o [ `GreeterClient` serviço do tutorial do gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) ( `Program.Main` ):</span><span class="sxs-lookup"><span data-stu-id="eca49-344">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

<span data-ttu-id="eca49-345">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="eca49-345">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="eca49-346">Coloque o `.proto` arquivo no `Shared` projeto da solução hospedada Blazor .</span><span class="sxs-lookup"><span data-stu-id="eca49-346">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="eca49-347">Um componente no aplicativo cliente pode fazer chamadas gRPC usando o cliente gRPC ( `Pages/Grpc.razor` ):</span><span class="sxs-lookup"><span data-stu-id="eca49-347">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

<span data-ttu-id="eca49-348">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="eca49-348">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="eca49-349">Para usar a `Status.DebugException` propriedade, use [Grpc .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) versão 2.30.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="eca49-349">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="eca49-350">Para obter mais informações, consulte <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="eca49-350">For more information, see <xref:grpc/browser>.</span></span>
