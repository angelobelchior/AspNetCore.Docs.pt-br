---
title: 'ASP.NET Core :::no-loc(Blazor WebAssembly)::: cenários de segurança adicionais'
author: guardrex
description: 'Saiba como configurar o :::no-loc(Blazor WebAssembly)::: para cenários de segurança adicionais.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 22c8ab52a93e7ea7df6be608501bebf33764b711
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055328"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="dbb66-103">ASP.NET Core :::no-loc(Blazor WebAssembly)::: cenários de segurança adicionais</span><span class="sxs-lookup"><span data-stu-id="dbb66-103">ASP.NET Core :::no-loc(Blazor WebAssembly)::: additional security scenarios</span></span>

<span data-ttu-id="dbb66-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dbb66-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="dbb66-105">Anexar tokens a solicitações de saída</span><span class="sxs-lookup"><span data-stu-id="dbb66-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="dbb66-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> é <xref:System.Net.Http.DelegatingHandler> usado para anexar tokens de acesso a instâncias de saída <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="dbb66-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> is a <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="dbb66-107">Os tokens são adquiridos usando o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> serviço, que é registrado pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="dbb66-107">Tokens are acquired using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, which is registered by the framework.</span></span> <span data-ttu-id="dbb66-108">Se um token não puder ser adquirido, um <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> será gerado.</span><span class="sxs-lookup"><span data-stu-id="dbb66-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="dbb66-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> tem um <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> método que pode ser usado para navegar pelo usuário para o provedor de identidade a fim de adquirir um novo token.</span><span class="sxs-lookup"><span data-stu-id="dbb66-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span>

<span data-ttu-id="dbb66-110">Para sua conveniência, a estrutura fornece o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> pré-configurado com o endereço base do aplicativo como uma URL autorizada.</span><span class="sxs-lookup"><span data-stu-id="dbb66-110">For convenience, the framework provides the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="dbb66-111">**Tokens de acesso são adicionados somente quando o URI de solicitação está dentro do URI base do aplicativo.**</span><span class="sxs-lookup"><span data-stu-id="dbb66-111">**Access tokens are only added when the request URI is within the app's base URI.**</span></span> <span data-ttu-id="dbb66-112">Quando os URIs de solicitação de saída não estiverem dentro do URI base do aplicativo, use uma [ `AuthorizationMessageHandler` classe personalizada ( *recomendado* )](#custom-authorizationmessagehandler-class) ou [Configure o `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span><span class="sxs-lookup"><span data-stu-id="dbb66-112">When outgoing request URIs aren't within the app's base URI, use a [custom `AuthorizationMessageHandler` class ( *recommended* )](#custom-authorizationmessagehandler-class) or [configure the `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span></span>

> [!NOTE]
> <span data-ttu-id="dbb66-113">Além da configuração do aplicativo cliente para acesso à API do servidor, a API do servidor também deve permitir solicitações entre origens (CORS) quando o cliente e o servidor não residem no mesmo endereço base.</span><span class="sxs-lookup"><span data-stu-id="dbb66-113">In addition to the client app configuration for server API access, the server API must also allow cross-origin requests (CORS) when the client and the server don't reside at the same base address.</span></span> <span data-ttu-id="dbb66-114">Para obter mais informações sobre a configuração CORS do lado do servidor, consulte a seção [CORS (compartilhamento de recursos entre origens)](#cross-origin-resource-sharing-cors) mais adiante neste artigo.</span><span class="sxs-lookup"><span data-stu-id="dbb66-114">For more information on server-side CORS configuration, see the [Cross-origin resource sharing (CORS)](#cross-origin-resource-sharing-cors) section later in this article.</span></span>

<span data-ttu-id="dbb66-115">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="dbb66-115">In the following example:</span></span>

* <span data-ttu-id="dbb66-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> Adiciona <xref:System.Net.Http.IHttpClientFactory> e serviços relacionados à coleção de serviços e configura um nome <xref:System.Net.Http.HttpClient> ( `ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="dbb66-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> adds <xref:System.Net.Http.IHttpClientFactory> and related services to the service collection and configures a named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span> <span data-ttu-id="dbb66-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> é o endereço base do URI do recurso ao enviar solicitações.</span><span class="sxs-lookup"><span data-stu-id="dbb66-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is the base address of the resource URI when sending requests.</span></span> <span data-ttu-id="dbb66-118"><xref:System.Net.Http.IHttpClientFactory> é fornecido pelo [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="dbb66-118"><xref:System.Net.Http.IHttpClientFactory> is provided by the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package.</span></span>
* <span data-ttu-id="dbb66-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> é <xref:System.Net.Http.DelegatingHandler> usado para anexar tokens de acesso a instâncias de saída <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="dbb66-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is the <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="dbb66-120">Tokens de acesso são adicionados somente quando o URI de solicitação está dentro do URI base do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dbb66-120">Access tokens are only added when the request URI is within the app's base URI.</span></span>
* <span data-ttu-id="dbb66-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> Cria e configura uma <xref:System.Net.Http.HttpClient> instância para solicitações de saída usando a configuração que corresponde ao nome <xref:System.Net.Http.HttpClient> ( `ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="dbb66-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> creates and configures an <xref:System.Net.Http.HttpClient> instance for outgoing requests using the configuration that corresponds to the named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span>

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

<span data-ttu-id="dbb66-122">Para um :::no-loc(Blazor)::: aplicativo baseado no :::no-loc(Blazor WebAssembly)::: modelo de projeto hospedado, os URIs de solicitação estão dentro do URI base do aplicativo por padrão.</span><span class="sxs-lookup"><span data-stu-id="dbb66-122">For a :::no-loc(Blazor)::: app based on the :::no-loc(Blazor WebAssembly)::: Hosted project template, request URIs are within the app's base URI by default.</span></span> <span data-ttu-id="dbb66-123">Portanto, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) é atribuído ao <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> em um aplicativo gerado a partir do modelo de projeto.</span><span class="sxs-lookup"><span data-stu-id="dbb66-123">Therefore, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in an app generated from the project template.</span></span>

<span data-ttu-id="dbb66-124">O configurado <xref:System.Net.Http.HttpClient> é usado para fazer solicitações autorizadas usando o [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) padrão:</span><span class="sxs-lookup"><span data-stu-id="dbb66-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="dbb66-125">`AuthorizationMessageHandler`Classe personalizada</span><span class="sxs-lookup"><span data-stu-id="dbb66-125">Custom `AuthorizationMessageHandler` class</span></span>

<span data-ttu-id="dbb66-126">*Estas diretrizes nesta seção são recomendadas para aplicativos cliente que fazem solicitações de saída para URIs que não estão dentro do URI base do aplicativo.*</span><span class="sxs-lookup"><span data-stu-id="dbb66-126">*This guidance in this section is recommended for client apps that make outgoing requests to URIs that aren't within the app's base URI.*</span></span>

<span data-ttu-id="dbb66-127">No exemplo a seguir, uma classe personalizada se estende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> para uso como <xref:System.Net.Http.DelegatingHandler> para um <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="dbb66-127">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> for use as the <xref:System.Net.Http.DelegatingHandler> for an <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="dbb66-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configura esse manipulador para autorizar solicitações HTTP de saída usando um token de acesso.</span><span class="sxs-lookup"><span data-stu-id="dbb66-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures this handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="dbb66-129">O token de acesso só será anexado se pelo menos uma das URLs autorizadas for uma base do URI de solicitação ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ).</span><span class="sxs-lookup"><span data-stu-id="dbb66-129">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span>

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

<span data-ttu-id="dbb66-130">No `Program.Main` ( `Program.cs` ), o `CustomAuthorizationMessageHandler` é registrado como um serviço com escopo e é configurado como o <xref:System.Net.Http.DelegatingHandler> para instâncias de saída <xref:System.Net.Http.HttpResponseMessage> feitas por um nome <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="dbb66-130">In `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` is registered as a scoped service and is configured as the <xref:System.Net.Http.DelegatingHandler> for outgoing <xref:System.Net.Http.HttpResponseMessage> instances made by a named <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="dbb66-131">Para um :::no-loc(Blazor)::: aplicativo baseado no :::no-loc(Blazor WebAssembly)::: modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) é atribuído ao <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> por padrão.</span><span class="sxs-lookup"><span data-stu-id="dbb66-131">For a :::no-loc(Blazor)::: app based on the :::no-loc(Blazor WebAssembly)::: Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="dbb66-132">O configurado <xref:System.Net.Http.HttpClient> é usado para fazer solicitações autorizadas usando o [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) padrão.</span><span class="sxs-lookup"><span data-stu-id="dbb66-132">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="dbb66-133">Em que o cliente é criado com <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pacote), as <xref:System.Net.Http.HttpClient> instâncias do são fornecidas que incluem tokens de acesso ao fazer solicitações para a API do servidor.</span><span class="sxs-lookup"><span data-stu-id="dbb66-133">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API.</span></span> <span data-ttu-id="dbb66-134">Se o URI de solicitação for um URI relativo, como no exemplo a seguir ( `ExampleAPIMethod` ), ele será combinado com o <xref:System.Net.Http.HttpClient.BaseAddress> quando o aplicativo cliente fizer a solicitação:</span><span class="sxs-lookup"><span data-stu-id="dbb66-134">If the request URI is a relative URI, as it is in the following example (`ExampleAPIMethod`), it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress> when the client app makes the request:</span></span>

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
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="dbb66-135">configurar `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="dbb66-135">Configure `AuthorizationMessageHandler`</span></span>

<span data-ttu-id="dbb66-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> pode ser configurado com URLs autorizadas, escopos e uma URL de retorno usando o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> método.</span><span class="sxs-lookup"><span data-stu-id="dbb66-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with authorized URLs, scopes, and a return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span> <span data-ttu-id="dbb66-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configura o manipulador para autorizar solicitações HTTP de saída usando um token de acesso.</span><span class="sxs-lookup"><span data-stu-id="dbb66-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures the handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="dbb66-138">O token de acesso só será anexado se pelo menos uma das URLs autorizadas for uma base do URI de solicitação ( <xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType> ).</span><span class="sxs-lookup"><span data-stu-id="dbb66-138">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span> <span data-ttu-id="dbb66-139">Se o URI de solicitação for um URI relativo, ele será combinado com o <xref:System.Net.Http.HttpClient.BaseAddress> .</span><span class="sxs-lookup"><span data-stu-id="dbb66-139">If the request URI is a relative URI, it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress>.</span></span>

<span data-ttu-id="dbb66-140">No exemplo a seguir, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configura um <xref:System.Net.Http.HttpClient> in `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="dbb66-140">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="dbb66-141">Para um :::no-loc(Blazor)::: aplicativo baseado no :::no-loc(Blazor WebAssembly)::: modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> é atribuído ao seguinte por padrão:</span><span class="sxs-lookup"><span data-stu-id="dbb66-141">For a :::no-loc(Blazor)::: app based on the :::no-loc(Blazor WebAssembly)::: Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="dbb66-142">O <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="dbb66-142">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="dbb66-143">Uma URL da `authorizedUrls` matriz.</span><span class="sxs-lookup"><span data-stu-id="dbb66-143">A URL of the `authorizedUrls` array.</span></span>

## <a name="typed-httpclient"></a><span data-ttu-id="dbb66-144">Automaticamente `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="dbb66-144">Typed `HttpClient`</span></span>

<span data-ttu-id="dbb66-145">Um cliente tipado pode ser definido para lidar com todas as preocupações de aquisição de token e HTTP em uma única classe.</span><span class="sxs-lookup"><span data-stu-id="dbb66-145">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="dbb66-146">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="dbb66-146">`WeatherForecastClient.cs`:</span></span>

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

<span data-ttu-id="dbb66-147">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `using static :::no-loc(Blazor):::Sample.Data;` ).</span><span class="sxs-lookup"><span data-stu-id="dbb66-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static :::no-loc(Blazor):::Sample.Data;`).</span></span>

<span data-ttu-id="dbb66-148">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="dbb66-148">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="dbb66-149">Para um :::no-loc(Blazor)::: aplicativo baseado no :::no-loc(Blazor WebAssembly)::: modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) é atribuído ao <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> por padrão.</span><span class="sxs-lookup"><span data-stu-id="dbb66-149">For a :::no-loc(Blazor)::: app based on the :::no-loc(Blazor WebAssembly)::: Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="dbb66-150">`FetchData` componente ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="dbb66-150">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="dbb66-151">Configurar o `HttpClient` manipulador</span><span class="sxs-lookup"><span data-stu-id="dbb66-151">Configure the `HttpClient` handler</span></span>

<span data-ttu-id="dbb66-152">O manipulador pode ser configurado ainda mais com <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="dbb66-152">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="dbb66-153">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="dbb66-153">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="dbb66-154">Para um :::no-loc(Blazor)::: aplicativo baseado no :::no-loc(Blazor WebAssembly)::: modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> é atribuído ao seguinte por padrão:</span><span class="sxs-lookup"><span data-stu-id="dbb66-154">For a :::no-loc(Blazor)::: app based on the :::no-loc(Blazor WebAssembly)::: Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="dbb66-155">O <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="dbb66-155">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="dbb66-156">Uma URL da `authorizedUrls` matriz.</span><span class="sxs-lookup"><span data-stu-id="dbb66-156">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="dbb66-157">Solicitações de API Web não autenticadas ou não autorizadas em um aplicativo com um cliente padrão seguro</span><span class="sxs-lookup"><span data-stu-id="dbb66-157">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="dbb66-158">Se o :::no-loc(Blazor WebAssembly)::: aplicativo normalmente usa um padrão seguro <xref:System.Net.Http.HttpClient> , o aplicativo também pode fazer solicitações de API da Web não autenticadas ou não autorizadas Configurando um nome <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="dbb66-158">If the :::no-loc(Blazor WebAssembly)::: app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="dbb66-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="dbb66-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="dbb66-160">Para um :::no-loc(Blazor)::: aplicativo baseado no :::no-loc(Blazor WebAssembly)::: modelo de projeto hospedado, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) é atribuído ao <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> por padrão.</span><span class="sxs-lookup"><span data-stu-id="dbb66-160">For a :::no-loc(Blazor)::: app based on the :::no-loc(Blazor WebAssembly)::: Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="dbb66-161">O registro anterior é além do registro padrão seguro existente <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="dbb66-161">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="dbb66-162">Um componente cria o <xref:System.Net.Http.HttpClient> do <xref:System.Net.Http.IHttpClientFactory> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pacote) para fazer solicitações não autenticadas ou não autorizadas:</span><span class="sxs-lookup"><span data-stu-id="dbb66-162">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="dbb66-163">O controlador na API do servidor, `WeatherForecastNoAuthenticationController` para o exemplo anterior, não está marcado com o [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="dbb66-163">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="dbb66-164">A decisão de usar um cliente seguro ou um cliente inseguro, pois a <xref:System.Net.Http.HttpClient> instância padrão é até o desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="dbb66-164">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="dbb66-165">Uma maneira de tomar essa decisão é considerar o número de pontos de extremidade autenticados versus não autenticados que o aplicativo contata.</span><span class="sxs-lookup"><span data-stu-id="dbb66-165">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="dbb66-166">Se a maioria das solicitações do aplicativo for proteger pontos de extremidade de API, use a instância autenticada <xref:System.Net.Http.HttpClient> como o padrão.</span><span class="sxs-lookup"><span data-stu-id="dbb66-166">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="dbb66-167">Caso contrário, registre a instância não autenticada <xref:System.Net.Http.HttpClient> como o padrão.</span><span class="sxs-lookup"><span data-stu-id="dbb66-167">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="dbb66-168">Uma abordagem alternativa para usar o <xref:System.Net.Http.IHttpClientFactory> é criar um [cliente tipado](#typed-httpclient) para acesso não autenticado a pontos de extremidade anônimos.</span><span class="sxs-lookup"><span data-stu-id="dbb66-168">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="dbb66-169">Solicitar tokens de acesso adicionais</span><span class="sxs-lookup"><span data-stu-id="dbb66-169">Request additional access tokens</span></span>

<span data-ttu-id="dbb66-170">Os tokens de acesso podem ser obtidos manualmente chamando `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="dbb66-170">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span> <span data-ttu-id="dbb66-171">No exemplo a seguir, um escopo adicional é exigido por um aplicativo para o padrão <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="dbb66-171">In the following example, an additional scope is required by an app for the default <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="dbb66-172">O exemplo da MSAL (biblioteca de autenticação da Microsoft) configura o escopo com `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="dbb66-172">The Microsoft Authentication Library (MSAL) example configures the scope with `MsalProviderOptions`:</span></span>

<span data-ttu-id="dbb66-173">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="dbb66-173">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

<span data-ttu-id="dbb66-174">Os `{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` espaços reservados e no exemplo anterior são Escopos personalizados.</span><span class="sxs-lookup"><span data-stu-id="dbb66-174">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="dbb66-175">O `IAccessTokenProvider.RequestToken` método fornece uma sobrecarga que permite que um aplicativo provisione um token de acesso com um determinado conjunto de escopos.</span><span class="sxs-lookup"><span data-stu-id="dbb66-175">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="dbb66-176">Em um :::no-loc(Razor)::: componente:</span><span class="sxs-lookup"><span data-stu-id="dbb66-176">In a :::no-loc(Razor)::: component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "{CUSTOM SCOPE 1}", "{CUSTOM SCOPE 2}" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="dbb66-177">Os `{CUSTOM SCOPE 1}` `{CUSTOM SCOPE 2}` espaços reservados e no exemplo anterior são Escopos personalizados.</span><span class="sxs-lookup"><span data-stu-id="dbb66-177">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="dbb66-178"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> apresenta</span><span class="sxs-lookup"><span data-stu-id="dbb66-178"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="dbb66-179">`true` com o `token` para uso.</span><span class="sxs-lookup"><span data-stu-id="dbb66-179">`true` with the `token` for use.</span></span>
* <span data-ttu-id="dbb66-180">`false` Se o token não for recuperado.</span><span class="sxs-lookup"><span data-stu-id="dbb66-180">`false` if the token isn't retrieved.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="dbb66-181">CORS (compartilhamento de recursos entre origens)</span><span class="sxs-lookup"><span data-stu-id="dbb66-181">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="dbb66-182">Ao enviar credenciais ( :::no-loc(cookie)::: s/cabeçalhos de autorização) em solicitações CORS, o `Authorization` cabeçalho deve ser permitido pela política CORS.</span><span class="sxs-lookup"><span data-stu-id="dbb66-182">When sending credentials (authorization :::no-loc(cookie):::s/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="dbb66-183">A política a seguir inclui a configuração para o:</span><span class="sxs-lookup"><span data-stu-id="dbb66-183">The following policy includes configuration for:</span></span>

* <span data-ttu-id="dbb66-184">Origens da solicitação ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="dbb66-184">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="dbb66-185">Qualquer método (verbo).</span><span class="sxs-lookup"><span data-stu-id="dbb66-185">Any method (verb).</span></span>
* <span data-ttu-id="dbb66-186">`Content-Type``Authorization`cabeçalhos e.</span><span class="sxs-lookup"><span data-stu-id="dbb66-186">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="dbb66-187">Para permitir um cabeçalho personalizado (por exemplo, `x-custom-header` ), liste o cabeçalho ao chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="dbb66-187">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="dbb66-188">Credenciais definidas pelo código JavaScript do lado do cliente ( `credentials` propriedade definida como `include` ).</span><span class="sxs-lookup"><span data-stu-id="dbb66-188">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="dbb66-189">Uma solução hospedada :::no-loc(Blazor)::: com base no :::no-loc(Blazor)::: modelo de projeto hospedado usa o mesmo endereço base para os aplicativos cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="dbb66-189">A hosted :::no-loc(Blazor)::: solution based on the :::no-loc(Blazor)::: Hosted project template uses the same base address for the client and server apps.</span></span> <span data-ttu-id="dbb66-190">O aplicativo cliente <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> é definido como um URI de `builder.HostEnvironment.BaseAddress` por padrão.</span><span class="sxs-lookup"><span data-stu-id="dbb66-190">The client app's <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is set to a URI of `builder.HostEnvironment.BaseAddress` by default.</span></span> <span data-ttu-id="dbb66-191">A configuração de CORS **não** é necessária na configuração padrão de um aplicativo hospedado criado a partir do :::no-loc(Blazor)::: modelo de projeto hospedado.</span><span class="sxs-lookup"><span data-stu-id="dbb66-191">CORS configuration is **not** required in the default configuration of a hosted app created from the :::no-loc(Blazor)::: Hosted project template.</span></span> <span data-ttu-id="dbb66-192">Aplicativos cliente adicionais que não são hospedados pelo projeto do servidor e que não compartilham **o endereço base do aplicativo do servidor** exigem a configuração CORS no projeto do servidor.</span><span class="sxs-lookup"><span data-stu-id="dbb66-192">Additional client apps that aren't hosted by the server project and don't share the server app's base address **do** require CORS configuration in the server project.</span></span>

<span data-ttu-id="dbb66-193">Para obter mais informações, consulte <xref:security/cors> e o componente testador de solicitação HTTP do aplicativo de exemplo ( `Components/HTTPRequestTester.razor` ).</span><span class="sxs-lookup"><span data-stu-id="dbb66-193">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="dbb66-194">Manipular erros de solicitação de token</span><span class="sxs-lookup"><span data-stu-id="dbb66-194">Handle token request errors</span></span>

<span data-ttu-id="dbb66-195">Quando um aplicativo de página única (SPA) autentica um usuário usando o OpenID Connect (OIDC), o estado de autenticação é mantido localmente no SPA e no :::no-loc(Identity)::: provedor (IP) na forma de uma sessão :::no-loc(cookie)::: definida como resultado do usuário que fornece suas credenciais.</span><span class="sxs-lookup"><span data-stu-id="dbb66-195">When a Single Page Application (SPA) authenticates a user using OpenID Connect (OIDC), the authentication state is maintained locally within the SPA and in the :::no-loc(Identity)::: Provider (IP) in the form of a session :::no-loc(cookie)::: that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="dbb66-196">Os tokens emitidos pelo IP para o usuário normalmente são válidos por curto período de tempo, cerca de uma hora normalmente, de modo que o aplicativo cliente deve buscar regularmente novos tokens.</span><span class="sxs-lookup"><span data-stu-id="dbb66-196">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="dbb66-197">Caso contrário, o usuário será desconectado após os tokens concedidos expirarem.</span><span class="sxs-lookup"><span data-stu-id="dbb66-197">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="dbb66-198">Na maioria dos casos, os clientes do OIDC são capazes de provisionar novos tokens sem exigir que o usuário se autentique novamente graças ao estado de autenticação ou "sessão" que é mantido dentro do IP.</span><span class="sxs-lookup"><span data-stu-id="dbb66-198">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="dbb66-199">Há alguns casos em que o cliente não pode obter um token sem interação do usuário, por exemplo, quando por algum motivo o usuário faz logoff explicitamente do IP.</span><span class="sxs-lookup"><span data-stu-id="dbb66-199">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="dbb66-200">Esse cenário ocorre se um usuário visita `https://login.microsoftonline.com` e faz logoff. Nesses cenários, o aplicativo não sabe imediatamente que o usuário fez logoff. Qualquer token que o cliente contém pode não ser mais válido.</span><span class="sxs-lookup"><span data-stu-id="dbb66-200">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="dbb66-201">Além disso, o cliente não é capaz de provisionar um novo token sem interação do usuário depois que o token atual expira.</span><span class="sxs-lookup"><span data-stu-id="dbb66-201">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="dbb66-202">Esses cenários não são específicos para a autenticação baseada em token.</span><span class="sxs-lookup"><span data-stu-id="dbb66-202">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="dbb66-203">Eles fazem parte da natureza do SPAs.</span><span class="sxs-lookup"><span data-stu-id="dbb66-203">They are part of the nature of SPAs.</span></span> <span data-ttu-id="dbb66-204">Um SPA usando :::no-loc(cookie)::: s também falha ao chamar uma API de servidor se a autenticação :::no-loc(cookie)::: for removida.</span><span class="sxs-lookup"><span data-stu-id="dbb66-204">An SPA using :::no-loc(cookie):::s also fails to call a server API if the authentication :::no-loc(cookie)::: is removed.</span></span>

<span data-ttu-id="dbb66-205">Quando um aplicativo executa chamadas à API para recursos protegidos, você deve estar atento ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="dbb66-205">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="dbb66-206">Para provisionar um novo token de acesso para chamar a API, o usuário pode ser solicitado a autenticar novamente.</span><span class="sxs-lookup"><span data-stu-id="dbb66-206">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="dbb66-207">Mesmo que o cliente tenha um token que pareça ser válido, a chamada para o servidor pode falhar porque o token foi revogado pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="dbb66-207">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="dbb66-208">Quando o aplicativo solicita um token, há dois resultados possíveis:</span><span class="sxs-lookup"><span data-stu-id="dbb66-208">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="dbb66-209">A solicitação é realizada com sucesso e o aplicativo tem um token válido.</span><span class="sxs-lookup"><span data-stu-id="dbb66-209">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="dbb66-210">A solicitação falha e o aplicativo deve autenticar o usuário novamente para obter um novo token.</span><span class="sxs-lookup"><span data-stu-id="dbb66-210">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="dbb66-211">Quando uma solicitação de token falha, você precisa decidir se deseja salvar qualquer estado atual antes de executar um redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="dbb66-211">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="dbb66-212">Existem várias abordagens com níveis crescentes de complexidade:</span><span class="sxs-lookup"><span data-stu-id="dbb66-212">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="dbb66-213">Armazene o estado da página atual no armazenamento de sessão.</span><span class="sxs-lookup"><span data-stu-id="dbb66-213">Store the current page state in session storage.</span></span> <span data-ttu-id="dbb66-214">Durante o [ `OnInitializedAsync` evento de ciclo de vida](xref:blazor/components/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), verifique se o estado pode ser restaurado antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="dbb66-214">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="dbb66-215">Adicione um parâmetro de cadeia de caracteres de consulta e use-o como uma maneira de sinalizar ao aplicativo que ele precisa Rehidratar o estado salvo anteriormente.</span><span class="sxs-lookup"><span data-stu-id="dbb66-215">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="dbb66-216">Adicione um parâmetro de cadeia de caracteres de consulta com um identificador exclusivo para armazenar dados no armazenamento de sessão sem risco de colisões com outros itens.</span><span class="sxs-lookup"><span data-stu-id="dbb66-216">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="dbb66-217">O exemplo a seguir mostra como:</span><span class="sxs-lookup"><span data-stu-id="dbb66-217">The following example shows how to:</span></span>

* <span data-ttu-id="dbb66-218">Preserve o estado antes de redirecionar para a página de logon.</span><span class="sxs-lookup"><span data-stu-id="dbb66-218">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="dbb66-219">Recupere o estado anterior depois da autenticação usando o parâmetro de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="dbb66-219">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="dbb66-220">Salvar o estado do aplicativo antes de uma operação de autenticação</span><span class="sxs-lookup"><span data-stu-id="dbb66-220">Save app state before an authentication operation</span></span>

<span data-ttu-id="dbb66-221">Durante uma operação de autenticação, há casos em que você deseja salvar o estado do aplicativo antes que o navegador seja redirecionado para o IP.</span><span class="sxs-lookup"><span data-stu-id="dbb66-221">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="dbb66-222">Esse pode ser o caso quando você estiver usando um contêiner de estado e quiser restaurar o estado depois que a autenticação for realizada com sucesso.</span><span class="sxs-lookup"><span data-stu-id="dbb66-222">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="dbb66-223">Você pode usar um objeto de estado de autenticação personalizado para preservar o estado específico do aplicativo ou uma referência a ele e restaurar esse estado depois que a operação de autenticação for concluída com êxito.</span><span class="sxs-lookup"><span data-stu-id="dbb66-223">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="dbb66-224">O exemplo a seguir demonstra a abordagem.</span><span class="sxs-lookup"><span data-stu-id="dbb66-224">The following example demonstrates the approach.</span></span>

<span data-ttu-id="dbb66-225">Uma classe de contêiner de estado é criada no aplicativo com propriedades para manter os valores de estado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dbb66-225">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="dbb66-226">No exemplo a seguir, o contêiner é usado para manter o valor do contador do componente padrão do modelo de projeto `Counter` ( `Pages/Counter.razor` ).</span><span class="sxs-lookup"><span data-stu-id="dbb66-226">In the following example, the container is used to maintain the counter value of the default project template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="dbb66-227">Os métodos para serialização e desserialização do contêiner se baseiam em <xref:System.Text.Json> .</span><span class="sxs-lookup"><span data-stu-id="dbb66-227">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

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

<span data-ttu-id="dbb66-228">O `Counter` componente usa o contêiner de estado para manter o `currentCount` valor fora do componente:</span><span class="sxs-lookup"><span data-stu-id="dbb66-228">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

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

<span data-ttu-id="dbb66-229">Crie um `ApplicationAuthenticationState` de <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> .</span><span class="sxs-lookup"><span data-stu-id="dbb66-229">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="dbb66-230">Forneça uma `Id` propriedade, que serve como um identificador para o estado armazenado localmente.</span><span class="sxs-lookup"><span data-stu-id="dbb66-230">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="dbb66-231">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="dbb66-231">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="dbb66-232">O `Authentication` componente ( `Pages/Authentication.razor` ) salva e restaura o estado do aplicativo usando o armazenamento de sessão local com os `StateContainer` métodos de serialização e desserialização, `GetStateForLocalStorage` e `SetStateFromLocalStorage` :</span><span class="sxs-lookup"><span data-stu-id="dbb66-232">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

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

<span data-ttu-id="dbb66-233">Este exemplo usa Azure Active Directory (AAD) para autenticação.</span><span class="sxs-lookup"><span data-stu-id="dbb66-233">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="dbb66-234">Em `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="dbb66-234">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="dbb66-235">O `ApplicationAuthenticationState` é configurado como o tipo de MSAL (biblioteca do Microsoft autenticação) `RemoteAuthenticationState` .</span><span class="sxs-lookup"><span data-stu-id="dbb66-235">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="dbb66-236">O contêiner de estado é registrado no contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="dbb66-236">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="dbb66-237">Personalizar rotas de aplicativo</span><span class="sxs-lookup"><span data-stu-id="dbb66-237">Customize app routes</span></span>

<span data-ttu-id="dbb66-238">Por padrão, a [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) biblioteca usa as rotas mostradas na tabela a seguir para representar Estados de autenticação diferentes.</span><span class="sxs-lookup"><span data-stu-id="dbb66-238">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="dbb66-239">Rota</span><span class="sxs-lookup"><span data-stu-id="dbb66-239">Route</span></span>                            | <span data-ttu-id="dbb66-240">Finalidade</span><span class="sxs-lookup"><span data-stu-id="dbb66-240">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="dbb66-241">Dispara uma operação de entrada.</span><span class="sxs-lookup"><span data-stu-id="dbb66-241">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="dbb66-242">Manipula o resultado de qualquer operação de entrada.</span><span class="sxs-lookup"><span data-stu-id="dbb66-242">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="dbb66-243">Exibe mensagens de erro quando a operação de entrada falha por algum motivo.</span><span class="sxs-lookup"><span data-stu-id="dbb66-243">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="dbb66-244">Dispara uma operação de saída.</span><span class="sxs-lookup"><span data-stu-id="dbb66-244">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="dbb66-245">Lida com o resultado de uma operação de saída.</span><span class="sxs-lookup"><span data-stu-id="dbb66-245">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="dbb66-246">Exibe mensagens de erro quando a operação de saída falha por algum motivo.</span><span class="sxs-lookup"><span data-stu-id="dbb66-246">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="dbb66-247">Indica que o usuário fez logoff com êxito.</span><span class="sxs-lookup"><span data-stu-id="dbb66-247">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="dbb66-248">Dispara uma operação para editar o perfil do usuário.</span><span class="sxs-lookup"><span data-stu-id="dbb66-248">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="dbb66-249">Dispara uma operação para registrar um novo usuário.</span><span class="sxs-lookup"><span data-stu-id="dbb66-249">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="dbb66-250">As rotas mostradas na tabela anterior são configuráveis via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="dbb66-250">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="dbb66-251">Ao definir opções para fornecer rotas personalizadas, confirme se o aplicativo tem uma rota que manipula cada caminho.</span><span class="sxs-lookup"><span data-stu-id="dbb66-251">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="dbb66-252">No exemplo a seguir, todos os caminhos são prefixados com `/security` .</span><span class="sxs-lookup"><span data-stu-id="dbb66-252">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="dbb66-253">`Authentication` componente ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="dbb66-253">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="dbb66-254">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="dbb66-254">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="dbb66-255">Se o requisito chamar caminhos completamente diferentes, defina as rotas conforme descrito anteriormente e processe o <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> com um parâmetro de ação explícita:</span><span class="sxs-lookup"><span data-stu-id="dbb66-255">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="dbb66-256">Você tem permissão para dividir a interface do usuário em páginas diferentes se optar por fazer isso.</span><span class="sxs-lookup"><span data-stu-id="dbb66-256">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="dbb66-257">Personalizar a interface do usuário de autenticação</span><span class="sxs-lookup"><span data-stu-id="dbb66-257">Customize the authentication user interface</span></span>

<span data-ttu-id="dbb66-258"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> inclui um conjunto padrão de partes da interface do usuário para cada Estado de autenticação.</span><span class="sxs-lookup"><span data-stu-id="dbb66-258"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="dbb66-259">Cada Estado pode ser personalizado passando um personalizado <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="dbb66-259">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="dbb66-260">Para personalizar o texto exibido durante o processo de logon inicial, o pode alterar o da <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="dbb66-260">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="dbb66-261">`Authentication` componente ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="dbb66-261">`Authentication` component (`Pages/Authentication.razor`):</span></span>

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

<span data-ttu-id="dbb66-262">O <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> tem um fragmento que pode ser usado por rota de autenticação mostrada na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="dbb66-262">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="dbb66-263">Rota</span><span class="sxs-lookup"><span data-stu-id="dbb66-263">Route</span></span>                            | <span data-ttu-id="dbb66-264">Fragmento</span><span class="sxs-lookup"><span data-stu-id="dbb66-264">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="dbb66-265">Personalizar o usuário</span><span class="sxs-lookup"><span data-stu-id="dbb66-265">Customize the user</span></span>

<span data-ttu-id="dbb66-266">Os usuários associados ao aplicativo podem ser personalizados.</span><span class="sxs-lookup"><span data-stu-id="dbb66-266">Users bound to the app can be customized.</span></span>

### <a name="customize-the-user-with-a-payload-claim"></a><span data-ttu-id="dbb66-267">Personalizar o usuário com uma declaração de carga</span><span class="sxs-lookup"><span data-stu-id="dbb66-267">Customize the user with a payload claim</span></span>

<span data-ttu-id="dbb66-268">No exemplo a seguir, os usuários autenticados do aplicativo recebem uma `amr` declaração para cada um dos métodos de autenticação do usuário.</span><span class="sxs-lookup"><span data-stu-id="dbb66-268">In the following example, the app's authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span> <span data-ttu-id="dbb66-269">A `amr` declaração identifica como o assunto do token foi autenticado nas declarações de :::no-loc(Identity)::: [carga](/azure/active-directory/develop/access-tokens#the-amr-claim)do Microsoft Platform v 1.0.</span><span class="sxs-lookup"><span data-stu-id="dbb66-269">The `amr` claim identifies how the subject of the token was authenticated in Microsoft :::no-loc(Identity)::: Platform v1.0 [payload claims](/azure/active-directory/develop/access-tokens#the-amr-claim).</span></span> <span data-ttu-id="dbb66-270">O exemplo usa uma classe de conta de usuário personalizada baseada em <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> .</span><span class="sxs-lookup"><span data-stu-id="dbb66-270">The example uses a custom user account class based on <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span></span>

<span data-ttu-id="dbb66-271">Crie uma classe que estenda a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> classe.</span><span class="sxs-lookup"><span data-stu-id="dbb66-271">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class.</span></span> <span data-ttu-id="dbb66-272">O exemplo a seguir define a `AuthenticationMethod` propriedade como a matriz do usuário de `amr` valores de propriedade JSON.</span><span class="sxs-lookup"><span data-stu-id="dbb66-272">The following example sets the `AuthenticationMethod` property to the user's array of `amr` JSON property values.</span></span> <span data-ttu-id="dbb66-273">`AuthenticationMethod` é preenchido automaticamente pela estrutura quando o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="dbb66-273">`AuthenticationMethod` is populated automatically by the framework when the user is authenticated.</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="dbb66-274">Crie uma fábrica que se estenda <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> para criar declarações dos métodos de autenticação do usuário armazenados no `CustomUserAccount.AuthenticationMethod` :</span><span class="sxs-lookup"><span data-stu-id="dbb66-274">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> to create claims from the user's authentication methods stored in `CustomUserAccount.AuthenticationMethod`:</span></span>

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

        if (initialUser.:::no-loc(Identity):::.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((Claims:::no-loc(Identity):::)initialUser.:::no-loc(Identity):::)
                    .AddClaim(new Claim("amr", value));
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="dbb66-275">Registre o `CustomAccountFactory` para o provedor de autenticação em uso.</span><span class="sxs-lookup"><span data-stu-id="dbb66-275">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="dbb66-276">Qualquer um dos seguintes registros é válido:</span><span class="sxs-lookup"><span data-stu-id="dbb66-276">Any of the following registrations are valid:</span></span>

* <span data-ttu-id="dbb66-277"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="dbb66-277"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="dbb66-278"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="dbb66-278"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="dbb66-279"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="dbb66-279"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a><span data-ttu-id="dbb66-280">Grupos de segurança do AAD e funções com uma classe de conta de usuário personalizada</span><span class="sxs-lookup"><span data-stu-id="dbb66-280">AAD security groups and roles with a custom user account class</span></span>

<span data-ttu-id="dbb66-281">Para obter um exemplo adicional que funciona com grupos de segurança do AAD e funções de administrador do AAD e uma classe de conta de usuário personalizada, consulte <xref:blazor/security/webassembly/aad-groups-roles> .</span><span class="sxs-lookup"><span data-stu-id="dbb66-281">For an additional example that works with AAD security groups and AAD Administrator Roles and a custom user account class, see <xref:blazor/security/webassembly/aad-groups-roles>.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="dbb66-282">Suporte ao pré-processamento com autenticação</span><span class="sxs-lookup"><span data-stu-id="dbb66-282">Support prerendering with authentication</span></span>

<span data-ttu-id="dbb66-283">Depois de seguir as diretrizes em um dos :::no-loc(Blazor WebAssembly)::: Tópicos do aplicativo hospedado, use as instruções a seguir para criar um aplicativo que:</span><span class="sxs-lookup"><span data-stu-id="dbb66-283">After following the guidance in one of the hosted :::no-loc(Blazor WebAssembly)::: app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="dbb66-284">Processa caminhos para os quais a autorização não é necessária.</span><span class="sxs-lookup"><span data-stu-id="dbb66-284">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="dbb66-285">Não PreRender caminhos para os quais a autorização é necessária.</span><span class="sxs-lookup"><span data-stu-id="dbb66-285">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="dbb66-286">Na *`Client`* classe do aplicativo `Program` ( `Program.cs` ), fatorar registros de serviço comuns em um método separado (por exemplo, `ConfigureCommonServices` ):</span><span class="sxs-lookup"><span data-stu-id="dbb66-286">In the *`Client`* app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="dbb66-287">No aplicativo do servidor `Startup.ConfigureServices` , registre os seguintes serviços adicionais:</span><span class="sxs-lookup"><span data-stu-id="dbb66-287">In the server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.Add:::no-loc(Razor):::Pages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="dbb66-288">No método do aplicativo do servidor `Startup.Configure` , substitua [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) por [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) :</span><span class="sxs-lookup"><span data-stu-id="dbb66-288">In the server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.:::no-loc(Razor):::PagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="dbb66-289">No aplicativo de servidor, crie uma `Pages` pasta se ela não existir.</span><span class="sxs-lookup"><span data-stu-id="dbb66-289">In the server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="dbb66-290">Crie uma `_Host.cshtml` página dentro da pasta do aplicativo do servidor `Pages` .</span><span class="sxs-lookup"><span data-stu-id="dbb66-290">Create a `_Host.cshtml` page inside the server app's `Pages` folder.</span></span> <span data-ttu-id="dbb66-291">Cole o conteúdo do *`Client`* arquivo do aplicativo `wwwroot/index.html` no `Pages/_Host.cshtml` arquivo.</span><span class="sxs-lookup"><span data-stu-id="dbb66-291">Paste the contents from the *`Client`* app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="dbb66-292">Atualize o conteúdo do arquivo:</span><span class="sxs-lookup"><span data-stu-id="dbb66-292">Update the file's contents:</span></span>

* <span data-ttu-id="dbb66-293">Adicione `@page "_Host"` ao topo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="dbb66-293">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="dbb66-294">Substitua a `<app>Loading...</app>` marca pela seguinte:</span><span class="sxs-lookup"><span data-stu-id="dbb66-294">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="dbb66-295">Opções para aplicativos hospedados e provedores de logon de terceiros</span><span class="sxs-lookup"><span data-stu-id="dbb66-295">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="dbb66-296">Ao autenticar e autorizar um :::no-loc(Blazor WebAssembly)::: aplicativo hospedado com um provedor de terceiros, há várias opções disponíveis para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="dbb66-296">When authenticating and authorizing a hosted :::no-loc(Blazor WebAssembly)::: app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="dbb66-297">O que você escolher dependerá de seu cenário.</span><span class="sxs-lookup"><span data-stu-id="dbb66-297">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="dbb66-298">Para obter mais informações, consulte <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="dbb66-298">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="dbb66-299">Autenticar usuários para chamar somente APIs de terceiros protegidas</span><span class="sxs-lookup"><span data-stu-id="dbb66-299">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="dbb66-300">Autentique o usuário com um fluxo OAuth do lado do cliente em relação ao provedor de API de terceiros:</span><span class="sxs-lookup"><span data-stu-id="dbb66-300">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="dbb66-301">Neste cenário:</span><span class="sxs-lookup"><span data-stu-id="dbb66-301">In this scenario:</span></span>

* <span data-ttu-id="dbb66-302">O servidor que hospeda o aplicativo não desempenha uma função.</span><span class="sxs-lookup"><span data-stu-id="dbb66-302">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="dbb66-303">As APIs no servidor não podem ser protegidas.</span><span class="sxs-lookup"><span data-stu-id="dbb66-303">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="dbb66-304">O aplicativo só pode chamar APIs de terceiros protegidas.</span><span class="sxs-lookup"><span data-stu-id="dbb66-304">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="dbb66-305">Autenticar usuários com um provedor de terceiros e chamar APIs protegidas no servidor host e terceiros</span><span class="sxs-lookup"><span data-stu-id="dbb66-305">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="dbb66-306">Configure :::no-loc(Identity)::: com um provedor de logon de terceiros.</span><span class="sxs-lookup"><span data-stu-id="dbb66-306">Configure :::no-loc(Identity)::: with a third-party login provider.</span></span> <span data-ttu-id="dbb66-307">Obtenha os tokens necessários para acesso à API de terceiros e armazene-os.</span><span class="sxs-lookup"><span data-stu-id="dbb66-307">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="dbb66-308">Quando um usuário faz logon, o :::no-loc(Identity)::: coleta tokens de acesso e de atualização como parte do processo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="dbb66-308">When a user logs in, :::no-loc(Identity)::: collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="dbb66-309">Nesse ponto, há algumas abordagens disponíveis para fazer chamadas à API para APIs de terceiros.</span><span class="sxs-lookup"><span data-stu-id="dbb66-309">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="dbb66-310">Usar um token de acesso do servidor para recuperar o token de acesso de terceiros</span><span class="sxs-lookup"><span data-stu-id="dbb66-310">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="dbb66-311">Use o token de acesso gerado no servidor para recuperar o token de acesso de terceiros de um ponto de extremidade de API de servidor.</span><span class="sxs-lookup"><span data-stu-id="dbb66-311">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="dbb66-312">A partir daí, use o token de acesso de terceiros para chamar recursos de API de terceiros diretamente do :::no-loc(Identity)::: no cliente.</span><span class="sxs-lookup"><span data-stu-id="dbb66-312">From there, use the third-party access token to call third-party API resources directly from :::no-loc(Identity)::: on the client.</span></span>

<span data-ttu-id="dbb66-313">Não recomendamos essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="dbb66-313">We don't recommend this approach.</span></span> <span data-ttu-id="dbb66-314">Essa abordagem requer tratar o token de acesso de terceiros como se ele fosse gerado para um cliente público.</span><span class="sxs-lookup"><span data-stu-id="dbb66-314">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="dbb66-315">Nos termos do OAuth, o aplicativo público não tem um segredo do cliente porque não pode ser confiável para armazenar segredos com segurança e o token de acesso é produzido para um cliente confidencial.</span><span class="sxs-lookup"><span data-stu-id="dbb66-315">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="dbb66-316">Um cliente confidencial é um cliente que tem um segredo do cliente e é considerado capaz de armazenar segredos com segurança.</span><span class="sxs-lookup"><span data-stu-id="dbb66-316">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="dbb66-317">O token de acesso de terceiros pode receber escopos adicionais para executar operações confidenciais com base no fato de que a terceira parte emitiu o token para um cliente mais confiável.</span><span class="sxs-lookup"><span data-stu-id="dbb66-317">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="dbb66-318">Da mesma forma, os tokens de atualização não devem ser emitidos para um cliente que não seja confiável, pois isso dá ao cliente acesso ilimitado, a menos que outras restrições sejam colocadas em vigor.</span><span class="sxs-lookup"><span data-stu-id="dbb66-318">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="dbb66-319">Fazer chamadas à API do cliente para a API do servidor para chamar APIs de terceiros</span><span class="sxs-lookup"><span data-stu-id="dbb66-319">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="dbb66-320">Faça uma chamada de API do cliente para a API do servidor.</span><span class="sxs-lookup"><span data-stu-id="dbb66-320">Make an API call from the client to the server API.</span></span> <span data-ttu-id="dbb66-321">No servidor, recupere o token de acesso para o recurso de API de terceiros e emita qualquer chamada necessária.</span><span class="sxs-lookup"><span data-stu-id="dbb66-321">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="dbb66-322">Embora essa abordagem exija um salto de rede extra por meio do servidor para chamar uma API de terceiros, ela finalmente resulta em uma experiência mais segura:</span><span class="sxs-lookup"><span data-stu-id="dbb66-322">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="dbb66-323">O servidor pode armazenar tokens de atualização e garantir que o aplicativo não perca o acesso a recursos de terceiros.</span><span class="sxs-lookup"><span data-stu-id="dbb66-323">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="dbb66-324">O aplicativo não pode vazar tokens de acesso do servidor que possa conter permissões mais confidenciais.</span><span class="sxs-lookup"><span data-stu-id="dbb66-324">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="dbb66-325">Usar pontos de extremidade do OpenID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="dbb66-325">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="dbb66-326">A biblioteca de autenticação e os :::no-loc(Blazor)::: modelos de projeto usam pontos de extremidade do OpenID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="dbb66-326">The authentication library and :::no-loc(Blazor)::: project templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="dbb66-327">Para usar um ponto de extremidade v 2.0, configure a opção de portador JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="dbb66-327">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="dbb66-328">No exemplo a seguir, o AAD está configurado para v 2.0 acrescentando um `v2.0` segmento à <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> Propriedade:</span><span class="sxs-lookup"><span data-stu-id="dbb66-328">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="dbb66-329">Como alternativa, a configuração pode ser feita no arquivo de configurações do aplicativo ( `:::no-loc(appsettings.json):::` ):</span><span class="sxs-lookup"><span data-stu-id="dbb66-329">Alternatively, the setting can be made in the app settings (`:::no-loc(appsettings.json):::`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="dbb66-330">Se a passagem de um segmento para a autoridade não for apropriada para o provedor de OIDC do aplicativo, como com provedores não AAD, defina a <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> propriedade diretamente.</span><span class="sxs-lookup"><span data-stu-id="dbb66-330">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="dbb66-331">Defina a propriedade no <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> ou no arquivo de configurações do aplicativo ( `:::no-loc(appsettings.json):::` ) com a `Authority` chave.</span><span class="sxs-lookup"><span data-stu-id="dbb66-331">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`:::no-loc(appsettings.json):::`) with the `Authority` key.</span></span>

<span data-ttu-id="dbb66-332">A lista de declarações no token de ID é alterada para pontos de extremidade v 2.0.</span><span class="sxs-lookup"><span data-stu-id="dbb66-332">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="dbb66-333">Para obter mais informações, consulte [por que atualizar para a plataforma Microsoft Identity (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="dbb66-333">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="dbb66-334">Configurar e usar o gRPC em componentes</span><span class="sxs-lookup"><span data-stu-id="dbb66-334">Configure and use gRPC in components</span></span>

<span data-ttu-id="dbb66-335">Para configurar um :::no-loc(Blazor WebAssembly)::: aplicativo para usar a [estrutura ASP.NET Core gRPC](xref:grpc/index):</span><span class="sxs-lookup"><span data-stu-id="dbb66-335">To configure a :::no-loc(Blazor WebAssembly)::: app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="dbb66-336">Habilite o gRPC-Web no servidor.</span><span class="sxs-lookup"><span data-stu-id="dbb66-336">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="dbb66-337">Para obter mais informações, consulte <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="dbb66-337">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="dbb66-338">Registre os serviços gRPC para o manipulador de mensagens do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="dbb66-338">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="dbb66-339">O exemplo a seguir configura o manipulador de mensagens de autorização do aplicativo para usar o [ `GreeterClient` serviço do tutorial do gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) ( `Program.Main` ):</span><span class="sxs-lookup"><span data-stu-id="dbb66-339">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

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

<span data-ttu-id="dbb66-340">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="dbb66-340">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span> <span data-ttu-id="dbb66-341">Coloque o `.proto` arquivo no `Shared` projeto da solução hospedada :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="dbb66-341">Place the `.proto` file in the `Shared` project of the hosted :::no-loc(Blazor)::: solution.</span></span>

<span data-ttu-id="dbb66-342">Um componente no aplicativo cliente pode fazer chamadas gRPC usando o cliente gRPC ( `Pages/Grpc.razor` ):</span><span class="sxs-lookup"><span data-stu-id="dbb66-342">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

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

<span data-ttu-id="dbb66-343">O espaço reservado `{APP ASSEMBLY}` é o nome do assembly do aplicativo (por exemplo, `:::no-loc(Blazor):::Sample` ).</span><span class="sxs-lookup"><span data-stu-id="dbb66-343">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span> <span data-ttu-id="dbb66-344">Para usar a `Status.DebugException` propriedade, use [Grpc .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) versão 2.30.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="dbb66-344">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="dbb66-345">Para obter mais informações, consulte <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="dbb66-345">For more information, see <xref:grpc/browser>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dbb66-346">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dbb66-346">Additional resources</span></span>

* <xref:blazor/security/webassembly/graph-api>
* [<span data-ttu-id="dbb66-347">`HttpClient` e `HttpRequestMessage` com opções de solicitação de API de busca</span><span class="sxs-lookup"><span data-stu-id="dbb66-347">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)
