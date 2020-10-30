---
title: 'Diferenças entre :::no-loc(SignalR)::: e ASP.NET Core :::no-loc(SignalR):::'
author: bradygaster
description: 'Diferenças entre :::no-loc(SignalR)::: e ASP.NET Core :::no-loc(SignalR):::'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
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
uid: signalr/version-differences
ms.openlocfilehash: c4c0ff83cb789e9aa35085496daa461404615726
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061204"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a><span data-ttu-id="ab5b5-103">Diferenças entre ASP.NET :::no-loc(SignalR)::: e ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="ab5b5-103">Differences between ASP.NET :::no-loc(SignalR)::: and ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="ab5b5-104">ASP.NET Core :::no-loc(SignalR)::: não é compatível com clientes ou servidores para ASP.NET :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ab5b5-104">ASP.NET Core :::no-loc(SignalR)::: isn't compatible with clients or servers for ASP.NET :::no-loc(SignalR):::.</span></span> <span data-ttu-id="ab5b5-105">Este artigo detalha os recursos que foram removidos ou alterados no ASP.NET Core :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ab5b5-105">This article details features which have been removed or changed in ASP.NET Core :::no-loc(SignalR):::.</span></span>

## <a name="how-to-identify-the-no-locsignalr-version"></a><span data-ttu-id="ab5b5-106">Como identificar a :::no-loc(SignalR)::: versão</span><span class="sxs-lookup"><span data-stu-id="ab5b5-106">How to identify the :::no-loc(SignalR)::: version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="ab5b5-107">ASP.NET :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="ab5b5-107">ASP.NET :::no-loc(SignalR):::</span></span> | <span data-ttu-id="ab5b5-108">ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="ab5b5-108">ASP.NET Core :::no-loc(SignalR):::</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="ab5b5-109">**Pacote NuGet do servidor**</span><span class="sxs-lookup"><span data-stu-id="ab5b5-109">**Server NuGet package**</span></span> | <span data-ttu-id="ab5b5-110">[Microsoft. AspNet.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::/)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-110">[Microsoft.AspNet.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::/)</span></span> | <span data-ttu-id="ab5b5-111">nenhuma.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-111">None.</span></span> <span data-ttu-id="ab5b5-112">Incluído na estrutura compartilhada [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="ab5b5-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="ab5b5-113">**Pacotes NuGet do cliente**</span><span class="sxs-lookup"><span data-stu-id="ab5b5-113">**Client NuGet packages**</span></span> | <span data-ttu-id="ab5b5-114">[Microsoft. AspNet. :::no-loc(SignalR)::: . Cliente](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.Client/)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-114">[Microsoft.AspNet.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.Client/)</span></span><br><span data-ttu-id="ab5b5-115">[Microsoft. AspNet. :::no-loc(SignalR)::: . JS](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.JS/)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-115">[Microsoft.AspNet.:::no-loc(SignalR):::.JS](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.JS/)</span></span> | <span data-ttu-id="ab5b5-116">[Microsoft. AspNetCore. :::no-loc(SignalR)::: . Cliente](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client/)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-116">[Microsoft.AspNetCore.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client/)</span></span> |
| <span data-ttu-id="ab5b5-117">**Pacote de NPM de cliente JavaScript**</span><span class="sxs-lookup"><span data-stu-id="ab5b5-117">**JavaScript client npm package**</span></span> | [<span data-ttu-id="ab5b5-118">signalr</span><span class="sxs-lookup"><span data-stu-id="ab5b5-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="ab5b5-119">**Cliente Java**</span><span class="sxs-lookup"><span data-stu-id="ab5b5-119">**Java client**</span></span> | <span data-ttu-id="ab5b5-120">[Repositório do GitHub](https://github.com/:::no-loc(SignalR):::/java-client) (preterido)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-120">[GitHub Repository](https://github.com/:::no-loc(SignalR):::/java-client) (deprecated)</span></span>  | <span data-ttu-id="ab5b5-121">Pacote Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="ab5b5-122">**Tipo de aplicativo do servidor**</span><span class="sxs-lookup"><span data-stu-id="ab5b5-122">**Server app type**</span></span> | <span data-ttu-id="ab5b5-123">ASP.NET (System. Web) ou OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="ab5b5-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="ab5b5-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab5b5-124">ASP.NET Core</span></span> |
| <span data-ttu-id="ab5b5-125">**Plataformas de servidor com suporte**</span><span class="sxs-lookup"><span data-stu-id="ab5b5-125">**Supported server platforms**</span></span> | <span data-ttu-id="ab5b5-126">.NET Framework 4,5 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ab5b5-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="ab5b5-127">.NET Core 3,0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ab5b5-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="ab5b5-128">ASP.NET :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="ab5b5-128">ASP.NET :::no-loc(SignalR):::</span></span> | <span data-ttu-id="ab5b5-129">ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="ab5b5-129">ASP.NET Core :::no-loc(SignalR):::</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="ab5b5-130">**Pacote NuGet do servidor**</span><span class="sxs-lookup"><span data-stu-id="ab5b5-130">**Server NuGet package**</span></span> | <span data-ttu-id="ab5b5-131">[Microsoft. AspNet.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::/)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-131">[Microsoft.AspNet.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::/)</span></span> | <span data-ttu-id="ab5b5-132">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="ab5b5-133">[Microsoft. AspNetCore.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::/)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-133">[Microsoft.AspNetCore.:::no-loc(SignalR):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::/)</span></span> <span data-ttu-id="ab5b5-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-134">(.NET Framework)</span></span> |
| <span data-ttu-id="ab5b5-135">**Pacotes NuGet do cliente**</span><span class="sxs-lookup"><span data-stu-id="ab5b5-135">**Client NuGet packages**</span></span> | <span data-ttu-id="ab5b5-136">[Microsoft. AspNet. :::no-loc(SignalR)::: . Cliente](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.Client/)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-136">[Microsoft.AspNet.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.Client/)</span></span><br><span data-ttu-id="ab5b5-137">[Microsoft. AspNet. :::no-loc(SignalR)::: . JS](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.JS/)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-137">[Microsoft.AspNet.:::no-loc(SignalR):::.JS](https://www.nuget.org/packages/Microsoft.AspNet.:::no-loc(SignalR):::.JS/)</span></span> | <span data-ttu-id="ab5b5-138">[Microsoft. AspNetCore. :::no-loc(SignalR)::: . Cliente](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client/)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-138">[Microsoft.AspNetCore.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client/)</span></span> |
| <span data-ttu-id="ab5b5-139">**Pacote de NPM de cliente JavaScript**</span><span class="sxs-lookup"><span data-stu-id="ab5b5-139">**JavaScript client npm package**</span></span> | [<span data-ttu-id="ab5b5-140">signalr</span><span class="sxs-lookup"><span data-stu-id="ab5b5-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="ab5b5-141">**Cliente Java**</span><span class="sxs-lookup"><span data-stu-id="ab5b5-141">**Java client**</span></span> | <span data-ttu-id="ab5b5-142">[Repositório do GitHub](https://github.com/:::no-loc(SignalR):::/java-client) (preterido)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-142">[GitHub Repository](https://github.com/:::no-loc(SignalR):::/java-client) (deprecated)</span></span>  | <span data-ttu-id="ab5b5-143">Pacote Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="ab5b5-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="ab5b5-144">**Tipo de aplicativo do servidor**</span><span class="sxs-lookup"><span data-stu-id="ab5b5-144">**Server app type**</span></span> | <span data-ttu-id="ab5b5-145">ASP.NET (System. Web) ou OWIN Self-Host</span><span class="sxs-lookup"><span data-stu-id="ab5b5-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="ab5b5-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab5b5-146">ASP.NET Core</span></span> |
| <span data-ttu-id="ab5b5-147">**Plataformas de servidor com suporte**</span><span class="sxs-lookup"><span data-stu-id="ab5b5-147">**Supported server platforms**</span></span> | <span data-ttu-id="ab5b5-148">.NET Framework 4,5 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ab5b5-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="ab5b5-149">.NET Framework 4.6.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ab5b5-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="ab5b5-150">.NET Core 2,1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ab5b5-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="ab5b5-151">Diferenças de recursos</span><span class="sxs-lookup"><span data-stu-id="ab5b5-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="ab5b5-152">Reconexões automáticas</span><span class="sxs-lookup"><span data-stu-id="ab5b5-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ab5b5-153">Em ASP.NET :::no-loc(SignalR)::: :</span><span class="sxs-lookup"><span data-stu-id="ab5b5-153">In ASP.NET :::no-loc(SignalR)::::</span></span>

* <span data-ttu-id="ab5b5-154">Por padrão, :::no-loc(SignalR)::: o tentará se reconectar ao servidor se a conexão for descartada.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-154">By default, :::no-loc(SignalR)::: attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="ab5b5-155">Em ASP.NET Core :::no-loc(SignalR)::: :</span><span class="sxs-lookup"><span data-stu-id="ab5b5-155">In ASP.NET Core :::no-loc(SignalR)::::</span></span>

* <span data-ttu-id="ab5b5-156">As reconexões automáticas são aceitas com o [cliente .net](xref:signalr/dotnet-client#automatically-reconnect) e o [cliente JavaScript](xref:signalr/javascript-client#automatically-reconnect):</span><span class="sxs-lookup"><span data-stu-id="ab5b5-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ab5b5-157">Antes do ASP.NET Core 3,0, o :::no-loc(SignalR)::: não oferece suporte a reconexões automáticas.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-157">Prior to ASP.NET Core 3.0, :::no-loc(SignalR)::: doesn't support automatic reconnects.</span></span> <span data-ttu-id="ab5b5-158">Se o cliente estiver desconectado, o usuário deverá iniciar explicitamente uma nova conexão para se reconectar.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="ab5b5-159">No ASP.NET :::no-loc(SignalR)::: , :::no-loc(SignalR)::: o tentará se reconectar ao servidor se a conexão for descartada.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-159">In ASP.NET :::no-loc(SignalR):::, :::no-loc(SignalR)::: attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="ab5b5-160">Suporte a protocolo</span><span class="sxs-lookup"><span data-stu-id="ab5b5-160">Protocol support</span></span>

<span data-ttu-id="ab5b5-161">ASP.NET Core :::no-loc(SignalR)::: dá suporte a JSON, bem como a um novo protocolo binário com base em [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="ab5b5-161">ASP.NET Core :::no-loc(SignalR)::: supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="ab5b5-162">Além disso, os protocolos personalizados podem ser criados.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="ab5b5-163">Transportes</span><span class="sxs-lookup"><span data-stu-id="ab5b5-163">Transports</span></span>

<span data-ttu-id="ab5b5-164">O transporte de quadro contínuo não tem suporte no ASP.NET Core :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ab5b5-164">The Forever Frame transport isn't supported in ASP.NET Core :::no-loc(SignalR):::.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="ab5b5-165">Diferenças no servidor</span><span class="sxs-lookup"><span data-stu-id="ab5b5-165">Differences on the server</span></span>

<span data-ttu-id="ab5b5-166">As :::no-loc(SignalR)::: bibliotecas do lado do servidor ASP.NET Core estão incluídas em [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), que é usado no modelo de **aplicativo Web do ASP.NET Core** para projetos do e do :::no-loc(Razor)::: MVC.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-166">The ASP.NET Core :::no-loc(SignalR)::: server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both :::no-loc(Razor)::: and MVC projects.</span></span>

<span data-ttu-id="ab5b5-167">ASP.NET Core :::no-loc(SignalR)::: é um middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-167">ASP.NET Core :::no-loc(SignalR)::: is an ASP.NET Core middleware.</span></span> <span data-ttu-id="ab5b5-168">Ele deve ser configurado chamando <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.Add:::no-loc(SignalR):::%2A> em `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ab5b5-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.Add:::no-loc(SignalR):::%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ab5b5-169">Para configurar o roteamento, mapeie as rotas para os hubs dentro da <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> chamada do método no `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="ab5b5-170">Para configurar o roteamento, mapeie as rotas para os hubs dentro da <xref:Microsoft.AspNetCore.Builder.:::no-loc(SignalR):::AppBuilderExtensions.Use:::no-loc(SignalR):::%2A> chamada do método no `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.:::no-loc(SignalR):::AppBuilderExtensions.Use:::no-loc(SignalR):::%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.Use:::no-loc(SignalR):::(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="ab5b5-171">Sessões adesivas</span><span class="sxs-lookup"><span data-stu-id="ab5b5-171">Sticky sessions</span></span>

<span data-ttu-id="ab5b5-172">O modelo de dimensionamento para ASP.NET :::no-loc(SignalR)::: permite que os clientes se reconectem e enviem mensagens para qualquer servidor no farm.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-172">The scaleout model for ASP.NET :::no-loc(SignalR)::: allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="ab5b5-173">No ASP.NET Core :::no-loc(SignalR)::: , o cliente deve interagir com o mesmo servidor durante a conexão.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-173">In ASP.NET Core :::no-loc(SignalR):::, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="ab5b5-174">Para expansão usando Redis, isso significa que as sessões adesivas são necessárias.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="ab5b5-175">Para o scale out usando o [ :::no-loc(SignalR)::: serviço do Azure](/azure/azure-signalr/), as sessões adesivas não são necessárias porque o serviço lida com conexões com os clientes.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-175">For scaleout using [Azure :::no-loc(SignalR)::: Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="ab5b5-176">Hub único por conexão</span><span class="sxs-lookup"><span data-stu-id="ab5b5-176">Single hub per connection</span></span>

<span data-ttu-id="ab5b5-177">No ASP.NET Core :::no-loc(SignalR)::: , o modelo de conexão foi simplificado.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-177">In ASP.NET Core :::no-loc(SignalR):::, the connection model has been simplified.</span></span> <span data-ttu-id="ab5b5-178">As conexões são feitas diretamente em um único Hub, em vez de uma única conexão sendo usada para compartilhar o acesso a vários hubs.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="ab5b5-179">Streaming</span><span class="sxs-lookup"><span data-stu-id="ab5b5-179">Streaming</span></span>

<span data-ttu-id="ab5b5-180">O ASP.NET Core :::no-loc(SignalR)::: agora dá suporte ao [streaming de dados](xref:signalr/streaming) do hub para o cliente.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-180">ASP.NET Core :::no-loc(SignalR)::: now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="ab5b5-181">Estado</span><span class="sxs-lookup"><span data-stu-id="ab5b5-181">State</span></span>

<span data-ttu-id="ab5b5-182">A capacidade de passar um estado arbitrário entre clientes e o Hub (geralmente chamado `HubState` ) foi removida, bem como suporte para mensagens de progresso.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="ab5b5-183">Não há nenhum equivalente de proxies de Hub no momento.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="ab5b5-184">Remoção de PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="ab5b5-184">PersistentConnection removal</span></span>

<span data-ttu-id="ab5b5-185">No ASP.NET Core :::no-loc(SignalR)::: , a classe [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) foi removida.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-185">In ASP.NET Core :::no-loc(SignalR):::, the [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="ab5b5-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="ab5b5-186">GlobalHost</span></span>

<span data-ttu-id="ab5b5-187">ASP.NET Core tem injeção de dependência (DI) incorporada à estrutura.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="ab5b5-188">Os serviços podem usar DI para acessar o [HubContext](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="ab5b5-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="ab5b5-189">O `GlobalHost` objeto usado em ASP.NET :::no-loc(SignalR)::: para obter um `HubContext` não existe em ASP.NET Core :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ab5b5-189">The `GlobalHost` object that is used in ASP.NET :::no-loc(SignalR)::: to get a `HubContext` doesn't exist in ASP.NET Core :::no-loc(SignalR):::.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="ab5b5-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="ab5b5-190">HubPipeline</span></span>

<span data-ttu-id="ab5b5-191">ASP.NET Core :::no-loc(SignalR)::: não tem suporte para `HubPipeline` módulos.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-191">ASP.NET Core :::no-loc(SignalR)::: doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="ab5b5-192">Diferenças no cliente</span><span class="sxs-lookup"><span data-stu-id="ab5b5-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="ab5b5-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="ab5b5-193">TypeScript</span></span>

<span data-ttu-id="ab5b5-194">O :::no-loc(SignalR)::: cliente ASP.NET Core é gravado em [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="ab5b5-194">The ASP.NET Core :::no-loc(SignalR)::: client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="ab5b5-195">Você pode escrever em JavaScript ou TypeScript ao usar o [cliente JavaScript](xref:signalr/javascript-client).</span><span class="sxs-lookup"><span data-stu-id="ab5b5-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="ab5b5-196">O cliente JavaScript está hospedado em NPM</span><span class="sxs-lookup"><span data-stu-id="ab5b5-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ab5b5-197">Em versões do ASP.NET, o cliente JavaScript foi obtido por meio de um pacote NuGet no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="ab5b5-198">Nas versões do ASP.NET Core, o [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) pacote NPM contém as bibliotecas JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="ab5b5-199">Este pacote não está incluído no modelo de **aplicativo Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="ab5b5-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="ab5b5-200">Use o NPM para obter e instalar o `@microsoft/signalr` pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="ab5b5-201">Em versões do ASP.NET, o cliente JavaScript foi obtido por meio de um pacote NuGet no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="ab5b5-202">Nas versões do ASP.NET Core, o [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) pacote NPM contém as bibliotecas JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="ab5b5-203">Este pacote não está incluído no modelo de **aplicativo Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="ab5b5-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="ab5b5-204">Use o NPM para obter e instalar o `@aspnet/signalr` pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="ab5b5-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="ab5b5-205">jQuery</span></span>

<span data-ttu-id="ab5b5-206">A dependência do jQuery foi removida, no entanto, os projetos ainda podem usar o jQuery.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="ab5b5-207">Suporte do Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="ab5b5-207">Internet Explorer support</span></span>

<span data-ttu-id="ab5b5-208">:::no-loc(SignalR):::O ASP.NET Core dá suporte ao Microsoft Internet Explorer 11 ou posterior, enquanto :::no-loc(SignalR)::: o ASP.net dá suporte ao Microsoft Internet Explorer 8 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-208">ASP.NET Core :::no-loc(SignalR)::: supports Microsoft Internet Explorer 11 or later, whereas ASP.NET :::no-loc(SignalR)::: supports Microsoft Internet Explorer 8 or later.</span></span>
<span data-ttu-id="ab5b5-209">Mais informações sobre o suporte ao navegador podem ser encontradas em [plataformas com suporte](xref:signalr/supported-platforms#javascript-client).</span><span class="sxs-lookup"><span data-stu-id="ab5b5-209">More info on browser support can be found at [supported platforms](xref:signalr/supported-platforms#javascript-client).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="ab5b5-210">Sintaxe do método de cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="ab5b5-210">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ab5b5-211">A sintaxe JavaScript foi alterada da versão ASP.NET do :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ab5b5-211">The JavaScript syntax has changed from the ASP.NET version of :::no-loc(SignalR):::.</span></span> <span data-ttu-id="ab5b5-212">Em vez de usar o `$connection` objeto, crie uma conexão usando a API [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="ab5b5-212">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="ab5b5-213">Use o método [on](/javascript/api/@microsoft/signalr/HubConnection#on) para especificar métodos de cliente que o Hub pode chamar.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-213">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="ab5b5-214">A sintaxe JavaScript foi alterada da versão ASP.NET do :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ab5b5-214">The JavaScript syntax has changed from the ASP.NET version of :::no-loc(SignalR):::.</span></span> <span data-ttu-id="ab5b5-215">Em vez de usar o `$connection` objeto, crie uma conexão usando a API [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="ab5b5-215">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="ab5b5-216">Use o método [on](/javascript/api/@aspnet/signalr/HubConnection#on) para especificar métodos de cliente que o Hub pode chamar.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-216">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="ab5b5-217">Depois de criar o método de cliente, inicie a conexão de Hub.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-217">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="ab5b5-218">Encadeamento um método [Catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) para registrar ou tratar erros.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-218">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="ab5b5-219">Proxies de Hub</span><span class="sxs-lookup"><span data-stu-id="ab5b5-219">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ab5b5-220">Os proxies do Hub não são mais gerados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-220">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="ab5b5-221">Em vez disso, o nome do método é passado para a API [Invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-221">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="ab5b5-222">Os proxies do Hub não são mais gerados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-222">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="ab5b5-223">Em vez disso, o nome do método é passado para a API [Invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-223">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="ab5b5-224">.NET e outros clientes</span><span class="sxs-lookup"><span data-stu-id="ab5b5-224">.NET and other clients</span></span>

<span data-ttu-id="ab5b5-225">O [Microsoft. AspNetCore. :::no-loc(SignalR)::: . ](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) O pacote NuGet do cliente contém as bibliotecas de cliente .net para ASP.NET Core :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="ab5b5-225">The [Microsoft.AspNetCore.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) NuGet package contains the .NET client libraries for ASP.NET Core :::no-loc(SignalR):::.</span></span>

<span data-ttu-id="ab5b5-226">Use o <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder> para criar e criar uma instância de uma conexão com um Hub.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-226">Use the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="ab5b5-227">Diferenças de dimensionamento</span><span class="sxs-lookup"><span data-stu-id="ab5b5-227">Scaleout differences</span></span>

<span data-ttu-id="ab5b5-228">ASP.NET :::no-loc(SignalR)::: dá suporte a SQL Server e Redis.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-228">ASP.NET :::no-loc(SignalR)::: supports SQL Server and Redis.</span></span> <span data-ttu-id="ab5b5-229">ASP.NET Core :::no-loc(SignalR)::: dá suporte ao serviço do Azure :::no-loc(SignalR)::: e Redis.</span><span class="sxs-lookup"><span data-stu-id="ab5b5-229">ASP.NET Core :::no-loc(SignalR)::: supports Azure :::no-loc(SignalR)::: Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="ab5b5-230">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="ab5b5-230">ASP.NET</span></span>

* [<span data-ttu-id="ab5b5-231">:::no-loc(SignalR)::: expansão com o barramento de serviço do Azure</span><span class="sxs-lookup"><span data-stu-id="ab5b5-231">:::no-loc(SignalR)::: scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="ab5b5-232">:::no-loc(SignalR)::: scale out com Redis</span><span class="sxs-lookup"><span data-stu-id="ab5b5-232">:::no-loc(SignalR)::: scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="ab5b5-233">:::no-loc(SignalR)::: dimensionamento com SQL Server</span><span class="sxs-lookup"><span data-stu-id="ab5b5-233">:::no-loc(SignalR)::: scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="ab5b5-234">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab5b5-234">ASP.NET Core</span></span>

* [<span data-ttu-id="ab5b5-235">Serviço do Azure :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="ab5b5-235">Azure :::no-loc(SignalR)::: Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="ab5b5-236">Redis backplane</span><span class="sxs-lookup"><span data-stu-id="ab5b5-236">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="ab5b5-237">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ab5b5-237">Additional resources</span></span>

* [<span data-ttu-id="ab5b5-238">Hubs</span><span class="sxs-lookup"><span data-stu-id="ab5b5-238">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="ab5b5-239">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="ab5b5-239">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="ab5b5-240">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="ab5b5-240">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="ab5b5-241">Plataformas com suporte</span><span class="sxs-lookup"><span data-stu-id="ab5b5-241">Supported platforms</span></span>](xref:signalr/supported-platforms)
