---
title: OWIN (Open Web Interface para .NET) com o ASP.NET Core
author: ardalis
description: Descubra como o ASP.NET Core dá suporte ao OWIN (Open Web Interface para .NET), que permite que aplicativos Web sejam desacoplados de servidores Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
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
uid: fundamentals/owin
ms.openlocfilehash: 6085abc45137223d7a676107cf06dc2cf97a5c19
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060671"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="794e3-103">OWIN (Open Web Interface para .NET) com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="794e3-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="794e3-104">Por [Steve Smith](https://ardalis.com/) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="794e3-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="794e3-105">O ASP.NET Core dá suporte para OWIN (Open Web Interface para .NET).</span><span class="sxs-lookup"><span data-stu-id="794e3-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="794e3-106">O OWIN permite que os aplicativos Web sejam separados dos servidores Web.</span><span class="sxs-lookup"><span data-stu-id="794e3-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="794e3-107">Ele define uma maneira padrão de usar o middleware em um pipeline para manipular solicitações e respostas associadas.</span><span class="sxs-lookup"><span data-stu-id="794e3-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="794e3-108">O middleware e os aplicativos ASP.NET Core podem interoperar com aplicativos, servidores e middleware baseados no OWIN.</span><span class="sxs-lookup"><span data-stu-id="794e3-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="794e3-109">O OWIN fornece uma camada de desacoplamento que permite duas estruturas com modelos de objeto diferentes para ser usadas juntas.</span><span class="sxs-lookup"><span data-stu-id="794e3-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="794e3-110">O pacote `Microsoft.AspNetCore.Owin` fornece duas implementações de adaptador:</span><span class="sxs-lookup"><span data-stu-id="794e3-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="794e3-111">ASP.NET Core para OWIN</span><span class="sxs-lookup"><span data-stu-id="794e3-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="794e3-112">OWIN para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="794e3-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="794e3-113">Isso permite que o ASP.NET Core seja hospedado em um servidor/host compatível com OWIN ou que outros componentes compatíveis com OWIN sejam executados no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="794e3-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="794e3-114">O uso desses adaptadores implica um custo de desempenho.</span><span class="sxs-lookup"><span data-stu-id="794e3-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="794e3-115">Os aplicativos que usam somente componentes do ASP.NET Core não devem usar o pacote `Microsoft.AspNetCore.Owin` ou os adaptadores.</span><span class="sxs-lookup"><span data-stu-id="794e3-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="794e3-116">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="794e3-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="794e3-117">Executando o middleware do OWIN no pipeline do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="794e3-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="794e3-118">O suporte ao OWIN do ASP.NET Core é implantado como parte do pacote `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="794e3-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="794e3-119">Importe o suporte ao OWIN para seu projeto instalando esse pacote.</span><span class="sxs-lookup"><span data-stu-id="794e3-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="794e3-120">O middleware do OWIN está em conformidade com a [especificação do OWIN](https://owin.org/spec/spec/owin-1.0.0.html), que exige uma interface `Func<IDictionary<string, object>, Task>` e a definição de chaves específicas (como `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="794e3-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="794e3-121">O seguinte middleware simples do OWIN exibe “Olá, Mundo”:</span><span class="sxs-lookup"><span data-stu-id="794e3-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="794e3-122">A assinatura de exemplo retorna uma `Task` e aceita uma `IDictionary<string, object>`, conforme solicitado pelo OWIN.</span><span class="sxs-lookup"><span data-stu-id="794e3-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="794e3-123">O código a seguir mostra como adicionar o middleware `OwinHello` (mostrado acima) ao pipeline do ASP.NET Core com o método de extensão `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="794e3-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="794e3-124">Configure outras ações a serem executadas no pipeline do OWIN.</span><span class="sxs-lookup"><span data-stu-id="794e3-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="794e3-125">Os cabeçalhos de resposta devem ser modificados apenas antes da primeira gravação no fluxo de resposta.</span><span class="sxs-lookup"><span data-stu-id="794e3-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="794e3-126">Não é recomendado fazer várias chamadas a `UseOwin` por motivos de desempenho.</span><span class="sxs-lookup"><span data-stu-id="794e3-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="794e3-127">Os componentes do OWIN funcionarão melhor se forem agrupados.</span><span class="sxs-lookup"><span data-stu-id="794e3-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="794e3-128">Executar o ASP.NET Core em um servidor baseado no OWIN e usar seu suporte do WebSockets</span><span class="sxs-lookup"><span data-stu-id="794e3-128">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="794e3-129">Outro exemplo de como os recursos dos servidores baseados no OWIN podem ser aproveitados pelo ASP.NET Core é o acesso a recursos como o WebSockets.</span><span class="sxs-lookup"><span data-stu-id="794e3-129">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="794e3-130">O servidor Web do OWIN no .NET usado no exemplo anterior tem suporte para Soquetes da Web internos, que podem ser aproveitados por um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="794e3-130">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="794e3-131">O exemplo abaixo mostra um aplicativo Web simples que dá suporte a Soquetes da Web e repete tudo o que foi enviado ao servidor por meio do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="794e3-131">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

## <a name="owin-environment"></a><span data-ttu-id="794e3-132">Ambiente do OWIN</span><span class="sxs-lookup"><span data-stu-id="794e3-132">OWIN environment</span></span>

<span data-ttu-id="794e3-133">Você pode construir um ambiente do OWIN usando o `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="794e3-133">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="794e3-134">Chaves do OWIN</span><span class="sxs-lookup"><span data-stu-id="794e3-134">OWIN keys</span></span>

<span data-ttu-id="794e3-135">O OWIN depende de um objeto `IDictionary<string,object>` para transmitir informações em uma troca de Solicitação/Resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="794e3-135">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="794e3-136">O ASP.NET Core implementa as chaves listadas abaixo.</span><span class="sxs-lookup"><span data-stu-id="794e3-136">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="794e3-137">Consulte a [especificação primária, extensões](https://owin.org/#spec) e as [Diretrizes de chaves do OWIN e chaves comuns](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="794e3-137">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="794e3-138">Dados de solicitação (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="794e3-138">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="794e3-139">Chave</span><span class="sxs-lookup"><span data-stu-id="794e3-139">Key</span></span>               | <span data-ttu-id="794e3-140">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="794e3-140">Value (type)</span></span> | <span data-ttu-id="794e3-141">Descrição</span><span class="sxs-lookup"><span data-stu-id="794e3-141">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="794e3-142">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="794e3-142">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="794e3-143">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="794e3-143">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="794e3-144">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="794e3-144">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="794e3-145">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="794e3-145">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="794e3-146">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="794e3-146">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="794e3-147">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="794e3-147">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="794e3-148">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="794e3-148">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="794e3-149">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="794e3-149">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="794e3-150">Dados de solicitação (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="794e3-150">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="794e3-151">Chave</span><span class="sxs-lookup"><span data-stu-id="794e3-151">Key</span></span>               | <span data-ttu-id="794e3-152">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="794e3-152">Value (type)</span></span> | <span data-ttu-id="794e3-153">Descrição</span><span class="sxs-lookup"><span data-stu-id="794e3-153">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="794e3-154">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="794e3-154">owin.RequestId</span></span> | `String` | <span data-ttu-id="794e3-155">Opcional</span><span class="sxs-lookup"><span data-stu-id="794e3-155">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="794e3-156">Dados de resposta (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="794e3-156">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="794e3-157">Chave</span><span class="sxs-lookup"><span data-stu-id="794e3-157">Key</span></span>               | <span data-ttu-id="794e3-158">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="794e3-158">Value (type)</span></span> | <span data-ttu-id="794e3-159">Descrição</span><span class="sxs-lookup"><span data-stu-id="794e3-159">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="794e3-160">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="794e3-160">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="794e3-161">Opcional</span><span class="sxs-lookup"><span data-stu-id="794e3-161">Optional</span></span> |
| <span data-ttu-id="794e3-162">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="794e3-162">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="794e3-163">Opcional</span><span class="sxs-lookup"><span data-stu-id="794e3-163">Optional</span></span> |
| <span data-ttu-id="794e3-164">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="794e3-164">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="794e3-165">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="794e3-165">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="794e3-166">Outros dados (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="794e3-166">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="794e3-167">Chave</span><span class="sxs-lookup"><span data-stu-id="794e3-167">Key</span></span>               | <span data-ttu-id="794e3-168">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="794e3-168">Value (type)</span></span> | <span data-ttu-id="794e3-169">Descrição</span><span class="sxs-lookup"><span data-stu-id="794e3-169">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="794e3-170">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="794e3-170">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="794e3-171">owin.Version</span><span class="sxs-lookup"><span data-stu-id="794e3-171">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="794e3-172">Chaves comuns</span><span class="sxs-lookup"><span data-stu-id="794e3-172">Common keys</span></span>

| <span data-ttu-id="794e3-173">Chave</span><span class="sxs-lookup"><span data-stu-id="794e3-173">Key</span></span>               | <span data-ttu-id="794e3-174">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="794e3-174">Value (type)</span></span> | <span data-ttu-id="794e3-175">Descrição</span><span class="sxs-lookup"><span data-stu-id="794e3-175">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="794e3-176">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="794e3-176">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="794e3-177">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="794e3-177">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="794e3-178">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="794e3-178">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="794e3-179">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="794e3-179">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="794e3-180">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="794e3-180">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="794e3-181">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="794e3-181">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="794e3-182">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="794e3-182">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="794e3-183">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="794e3-183">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="794e3-184">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="794e3-184">SendFiles v0.3.0</span></span>

| <span data-ttu-id="794e3-185">Chave</span><span class="sxs-lookup"><span data-stu-id="794e3-185">Key</span></span>               | <span data-ttu-id="794e3-186">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="794e3-186">Value (type)</span></span> | <span data-ttu-id="794e3-187">Descrição</span><span class="sxs-lookup"><span data-stu-id="794e3-187">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="794e3-188">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="794e3-188">sendfile.SendAsync</span></span> | <span data-ttu-id="794e3-189">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="794e3-189">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="794e3-190">Por solicitação</span><span class="sxs-lookup"><span data-stu-id="794e3-190">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="794e3-191">Opaque v0.3.0</span><span class="sxs-lookup"><span data-stu-id="794e3-191">Opaque v0.3.0</span></span>

| <span data-ttu-id="794e3-192">Chave</span><span class="sxs-lookup"><span data-stu-id="794e3-192">Key</span></span>               | <span data-ttu-id="794e3-193">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="794e3-193">Value (type)</span></span> | <span data-ttu-id="794e3-194">Descrição</span><span class="sxs-lookup"><span data-stu-id="794e3-194">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="794e3-195">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="794e3-195">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="794e3-196">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="794e3-196">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="794e3-197">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="794e3-197">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="794e3-198">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="794e3-198">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="794e3-199">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="794e3-199">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="794e3-200">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="794e3-200">WebSocket v0.3.0</span></span>

| <span data-ttu-id="794e3-201">Chave</span><span class="sxs-lookup"><span data-stu-id="794e3-201">Key</span></span>               | <span data-ttu-id="794e3-202">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="794e3-202">Value (type)</span></span> | <span data-ttu-id="794e3-203">Descrição</span><span class="sxs-lookup"><span data-stu-id="794e3-203">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="794e3-204">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="794e3-204">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="794e3-205">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="794e3-205">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="794e3-206">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="794e3-206">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="794e3-207">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="794e3-207">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="794e3-208">Sem especificação</span><span class="sxs-lookup"><span data-stu-id="794e3-208">Non-spec</span></span> |
| <span data-ttu-id="794e3-209">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="794e3-209">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="794e3-210">Consulte a etapa 5.5 [RFC6455 Seção 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2)</span><span class="sxs-lookup"><span data-stu-id="794e3-210">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="794e3-211">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="794e3-211">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="794e3-212">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="794e3-212">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="794e3-213">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="794e3-213">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="794e3-214">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="794e3-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="794e3-215">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="794e3-215">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="794e3-216">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="794e3-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="794e3-217">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="794e3-217">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="794e3-218">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="794e3-218">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="794e3-219">Opcional</span><span class="sxs-lookup"><span data-stu-id="794e3-219">Optional</span></span> |
| <span data-ttu-id="794e3-220">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="794e3-220">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="794e3-221">Opcional</span><span class="sxs-lookup"><span data-stu-id="794e3-221">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="794e3-222">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="794e3-222">Additional resources</span></span>

* [<span data-ttu-id="794e3-223">Middleware</span><span class="sxs-lookup"><span data-stu-id="794e3-223">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="794e3-224">Servidores</span><span class="sxs-lookup"><span data-stu-id="794e3-224">Servers</span></span>](xref:fundamentals/servers/index)
