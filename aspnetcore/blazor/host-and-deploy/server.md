---
title: 'Hospedar e implantar ASP.NET Core Blazor Server'
author: guardrex
description: 'Saiba como hospedar e implantar um Blazor Server aplicativo usando o ASP.NET Core.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/host-and-deploy/server
ms.openlocfilehash: a209109210ef5e335734a974ceb0c2af7cb8e1a1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595435"
---
# <a name="host-and-deploy-no-locblazor-server"></a><span data-ttu-id="3c421-103">Hospedar e implantar Blazor Server</span><span class="sxs-lookup"><span data-stu-id="3c421-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="3c421-104">Por [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3c421-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="3c421-105">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="3c421-105">Host configuration values</span></span>

<span data-ttu-id="3c421-106">os [ Blazor Server aplicativos](xref:blazor/hosting-models#blazor-server) podem aceitar [valores de configuração de host genéricos](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="3c421-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="3c421-107">Implantação</span><span class="sxs-lookup"><span data-stu-id="3c421-107">Deployment</span></span>

<span data-ttu-id="3c421-108">Usando o [ Blazor Server modelo de hospedagem](xref:blazor/hosting-models#blazor-server), o Blazor é executado no servidor de dentro de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c421-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="3c421-109">As atualizações de interface do usuário, manipulação de eventos e chamadas JavaScript são manipuladas por uma [SignalR](xref:signalr/introduction) conexão.</span><span class="sxs-lookup"><span data-stu-id="3c421-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="3c421-110">É necessário um servidor Web capaz de hospedar um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c421-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="3c421-111">O Visual Studio inclui o modelo de projeto de **Blazor Server aplicativo** ( `blazorserverside` modelo ao usar o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando).</span><span class="sxs-lookup"><span data-stu-id="3c421-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="3c421-112">Escalabilidade</span><span class="sxs-lookup"><span data-stu-id="3c421-112">Scalability</span></span>

<span data-ttu-id="3c421-113">Planeje uma implantação para fazer o melhor uso da infraestrutura disponível para um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3c421-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="3c421-114">Consulte os seguintes recursos para abordar a Blazor Server escalabilidade do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="3c421-114">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="3c421-115">Conceitos básicos de Blazor Server aplicativos</span><span class="sxs-lookup"><span data-stu-id="3c421-115">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="3c421-116">Servidor de implantação</span><span class="sxs-lookup"><span data-stu-id="3c421-116">Deployment server</span></span>

<span data-ttu-id="3c421-117">Ao considerar a escalabilidade de um único servidor (escalar verticalmente), a memória disponível para um aplicativo provavelmente será o primeiro recurso que o aplicativo esgotará conforme as demandas do usuário aumentarem.</span><span class="sxs-lookup"><span data-stu-id="3c421-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="3c421-118">A memória disponível no servidor afeta o:</span><span class="sxs-lookup"><span data-stu-id="3c421-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="3c421-119">Número de circuitos ativos aos quais um servidor pode dar suporte.</span><span class="sxs-lookup"><span data-stu-id="3c421-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="3c421-120">Latência da interface do usuário no cliente.</span><span class="sxs-lookup"><span data-stu-id="3c421-120">UI latency on the client.</span></span>

<span data-ttu-id="3c421-121">Para obter orientação sobre a criação de Blazor aplicativos de servidor seguros e escalonáveis, consulte <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="3c421-121">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="3c421-122">Cada circuito usa aproximadamente 250 KB de memória para um aplicativo estilo mínimo de *Olá, mundo*.</span><span class="sxs-lookup"><span data-stu-id="3c421-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World* -style app.</span></span> <span data-ttu-id="3c421-123">O tamanho de um circuito depende do código do aplicativo e dos requisitos de manutenção de estado associados a cada componente.</span><span class="sxs-lookup"><span data-stu-id="3c421-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="3c421-124">Recomendamos que você meça as demandas de recursos durante o desenvolvimento para seu aplicativo e infraestrutura, mas a linha de base a seguir pode ser um ponto de partida para planejar seu destino de implantação: se você espera que seu aplicativo ofereça suporte a 5.000 usuários simultâneos, considere o orçamento de pelo menos 1,3 GB de memória do servidor para o aplicativo (ou ~ 273 KB por usuário).</span><span class="sxs-lookup"><span data-stu-id="3c421-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="no-locsignalr-configuration"></a><span data-ttu-id="3c421-125">SignalR configuração</span><span class="sxs-lookup"><span data-stu-id="3c421-125">SignalR configuration</span></span>

<span data-ttu-id="3c421-126">Blazor Server os aplicativos usam ASP.NET Core SignalR para se comunicar com o navegador.</span><span class="sxs-lookup"><span data-stu-id="3c421-126">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="3c421-127">as [ SignalR condições de hospedagem e dimensionamento do](xref:signalr/publish-to-azure-web-app) se aplicam aos Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="3c421-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="3c421-128">Blazor funciona melhor ao usar o WebSocket como SignalR transporte devido à latência, confiabilidade e [segurança](xref:signalr/security)menores.</span><span class="sxs-lookup"><span data-stu-id="3c421-128">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="3c421-129">A sondagem longa é usada pelo SignalR quando o WebSockets não está disponível ou quando o aplicativo é explicitamente configurado para usar sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="3c421-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="3c421-130">Ao implantar no serviço Azure App, configure o aplicativo para usar Websockets nas configurações de portal do Azure para o serviço.</span><span class="sxs-lookup"><span data-stu-id="3c421-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="3c421-131">Para obter detalhes sobre como configurar o aplicativo para Azure App serviço, consulte as [ SignalR diretrizes de publicação](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="3c421-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-no-locsignalr-service"></a><span data-ttu-id="3c421-132">Serviço do Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="3c421-132">Azure SignalR Service</span></span>

<span data-ttu-id="3c421-133">É recomendável usar [o SignalR serviço do Azure](xref:signalr/scale#azure-signalr-service) para Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="3c421-133">We recommend using the [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) for Blazor Server apps.</span></span> <span data-ttu-id="3c421-134">O serviço permite escalar verticalmente um Blazor Server aplicativo para um grande número de conexões simultâneas SignalR .</span><span class="sxs-lookup"><span data-stu-id="3c421-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="3c421-135">Além disso, o SignalR alcance global do serviço e os data centers de alto desempenho auxiliam significativamente na redução da latência devido à geografia.</span><span class="sxs-lookup"><span data-stu-id="3c421-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3c421-136">Quando os [WebSockets](https://wikipedia.org/wiki/WebSocket) estão desabilitados, Azure app serviço simula uma conexão em tempo real usando a sondagem longa http.</span><span class="sxs-lookup"><span data-stu-id="3c421-136">When [WebSockets](https://wikipedia.org/wiki/WebSocket) are disabled, Azure App Service simulates a real-time connection using HTTP Long Polling.</span></span> <span data-ttu-id="3c421-137">A sondagem longa HTTP é visivelmente mais lenta do que a execução com WebSockets habilitados, o que não usa a sondagem para simular uma conexão cliente-servidor.</span><span class="sxs-lookup"><span data-stu-id="3c421-137">HTTP Long Polling is noticeably slower than running with WebSockets enabled, which doesn't use polling to simulate a client-server connection.</span></span>
>
> <span data-ttu-id="3c421-138">É recomendável usar Websockets para Blazor Server aplicativos implantados no serviço Azure app.</span><span class="sxs-lookup"><span data-stu-id="3c421-138">We recommend using WebSockets for Blazor Server apps deployed to Azure App Service.</span></span> <span data-ttu-id="3c421-139">O [ SignalR serviço do Azure](xref:signalr/scale#azure-signalr-service) usa o WebSocket por padrão.</span><span class="sxs-lookup"><span data-stu-id="3c421-139">The [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) uses WebSockets by default.</span></span> <span data-ttu-id="3c421-140">Se o aplicativo não usar o serviço do Azure SignalR , consulte <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> .</span><span class="sxs-lookup"><span data-stu-id="3c421-140">If the app doesn't use the Azure SignalR Service, see <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>.</span></span>
>
> <span data-ttu-id="3c421-141">Para obter mais informações, consulte:</span><span class="sxs-lookup"><span data-stu-id="3c421-141">For more information, see:</span></span>
>
> * [<span data-ttu-id="3c421-142">O que é o serviço do Azure SignalR ?</span><span class="sxs-lookup"><span data-stu-id="3c421-142">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
> * [<span data-ttu-id="3c421-143">Guia de desempenho do SignalR serviço do Azure</span><span class="sxs-lookup"><span data-stu-id="3c421-143">Performance guide for Azure SignalR Service</span></span>](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a><span data-ttu-id="3c421-144">Configuração</span><span class="sxs-lookup"><span data-stu-id="3c421-144">Configuration</span></span>

<span data-ttu-id="3c421-145">Para configurar um aplicativo para o serviço do Azure SignalR , o aplicativo deve dar suporte a *sessões adesivas* , em que os clientes são [redirecionados de volta para o mesmo servidor durante o pré-processamento](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="3c421-145">To configure an app for the Azure SignalR Service, the app must support *sticky sessions* , where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="3c421-146">A `ServerStickyMode` opção ou o valor de configuração é definido como `Required` .</span><span class="sxs-lookup"><span data-stu-id="3c421-146">The `ServerStickyMode` option or configuration value is set to `Required`.</span></span> <span data-ttu-id="3c421-147">Normalmente, um aplicativo cria a configuração usando **_uma_** das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="3c421-147">Typically, an app creates the configuration using **_ONE_** of the following approaches:</span></span>

   * <span data-ttu-id="3c421-148">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3c421-148">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="3c421-149">Configuração (use **_uma_** das seguintes abordagens):</span><span class="sxs-lookup"><span data-stu-id="3c421-149">Configuration (use **_ONE_** of the following approaches):</span></span>
  
     * <span data-ttu-id="3c421-150">Em `appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="3c421-150">In `appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     * <span data-ttu-id="3c421-151">As configurações do aplicativo de **configuração** do serviço de aplicativo  >  **Application settings** na portal do Azure ( **nome** : `Azure__SignalR__StickyServerMode` , **valor** : `Required` ).</span><span class="sxs-lookup"><span data-stu-id="3c421-151">The app service's **Configuration** > **Application settings** in the Azure portal ( **Name** : `Azure__SignalR__StickyServerMode`, **Value** : `Required`).</span></span> <span data-ttu-id="3c421-152">Essa abordagem será adotada para o aplicativo automaticamente se você [provisionar o SignalR serviço do Azure](#provision-the-azure-signalr-service).</span><span class="sxs-lookup"><span data-stu-id="3c421-152">This approach is adopted for the app automatically if you [provision the Azure SignalR Service](#provision-the-azure-signalr-service).</span></span>

### <a name="provision-the-azure-no-locsignalr-service"></a><span data-ttu-id="3c421-153">Provisionar o serviço do Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="3c421-153">Provision the Azure SignalR Service</span></span>

<span data-ttu-id="3c421-154">Para provisionar o serviço do Azure SignalR para um aplicativo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="3c421-154">To provision the Azure SignalR Service for an app in Visual Studio:</span></span>

1. <span data-ttu-id="3c421-155">Crie um perfil de publicação de aplicativos do Azure no Visual Studio para o Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3c421-155">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="3c421-156">Adicione a dependência de **SignalR serviço do Azure** ao perfil.</span><span class="sxs-lookup"><span data-stu-id="3c421-156">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="3c421-157">Se a assinatura do Azure não tiver uma instância de serviço do Azure já existente SignalR para atribuir ao aplicativo, selecione **criar uma nova SignalR instância de serviço do Azure** para provisionar uma nova instância de serviço.</span><span class="sxs-lookup"><span data-stu-id="3c421-157">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="3c421-158">Publicar o aplicativo no Azure.</span><span class="sxs-lookup"><span data-stu-id="3c421-158">Publish the app to Azure.</span></span>

<span data-ttu-id="3c421-159">O provisionamento do serviço do Azure SignalR no Visual Studio habilita automaticamente as [ *sessões adesivas*](#configuration) e adiciona a SignalR cadeia de conexão à configuração do serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3c421-159">Provisioning the Azure SignalR Service in Visual Studio automatically [enables *sticky sessions*](#configuration) and adds the SignalR connection string to the app service's configuration.</span></span>

#### <a name="iis"></a><span data-ttu-id="3c421-160">IIS</span><span class="sxs-lookup"><span data-stu-id="3c421-160">IIS</span></span>

<span data-ttu-id="3c421-161">Ao usar o IIS, habilite:</span><span class="sxs-lookup"><span data-stu-id="3c421-161">When using IIS, enable:</span></span>

* <span data-ttu-id="3c421-162">[WebSockets no IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="3c421-162">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="3c421-163">[Sessões adesivas com Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="3c421-163">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="3c421-164">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="3c421-164">Kubernetes</span></span>

<span data-ttu-id="3c421-165">Crie uma definição de entrada com as seguintes [anotações de kubernetes para sessões adesivas](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="3c421-165">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="3c421-166">Linux com o Nginx</span><span class="sxs-lookup"><span data-stu-id="3c421-166">Linux with Nginx</span></span>

<span data-ttu-id="3c421-167">Para SignalR que o WebSockets funcione corretamente, confirme se os `Upgrade` cabeçalhos e o proxy `Connection` estão definidos com os seguintes valores e se estão `$connection_upgrade` mapeados para:</span><span class="sxs-lookup"><span data-stu-id="3c421-167">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="3c421-168">O valor do cabeçalho de atualização por padrão.</span><span class="sxs-lookup"><span data-stu-id="3c421-168">The Upgrade header value by default.</span></span>
* <span data-ttu-id="3c421-169">`close` Quando o cabeçalho de atualização está ausente ou vazio.</span><span class="sxs-lookup"><span data-stu-id="3c421-169">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="3c421-170">Para obter mais informações, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="3c421-170">For more information, see the following articles:</span></span>

* [<span data-ttu-id="3c421-171">NGINX como um proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="3c421-171">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="3c421-172">Proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="3c421-172">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="3c421-173">Linux com o Apache</span><span class="sxs-lookup"><span data-stu-id="3c421-173">Linux with Apache</span></span>

<span data-ttu-id="3c421-174">Para hospedar um Blazor aplicativo por trás do Apache no Linux, configure `ProxyPass` para tráfego HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3c421-174">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="3c421-175">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="3c421-175">In the following example:</span></span>

* <span data-ttu-id="3c421-176">O servidor Kestrel está em execução no computador host.</span><span class="sxs-lookup"><span data-stu-id="3c421-176">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="3c421-177">O aplicativo escuta o tráfego na porta 5000.</span><span class="sxs-lookup"><span data-stu-id="3c421-177">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="3c421-178">Habilite os seguintes módulos:</span><span class="sxs-lookup"><span data-stu-id="3c421-178">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="3c421-179">Verifique se há erros de WebSocket no console do navegador.</span><span class="sxs-lookup"><span data-stu-id="3c421-179">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="3c421-180">Erros de exemplo:</span><span class="sxs-lookup"><span data-stu-id="3c421-180">Example errors:</span></span>

* <span data-ttu-id="3c421-181">O Firefox não pode estabelecer uma conexão com o servidor em ws://the-domain-name.tld/_blazor?id=XXX.</span><span class="sxs-lookup"><span data-stu-id="3c421-181">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="3c421-182">Erro: falha ao iniciar o transporte ' WebSockets ': erro: erro no transporte.</span><span class="sxs-lookup"><span data-stu-id="3c421-182">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="3c421-183">Erro: falha ao iniciar o transporte ' LongPolling ': TypeError: this. Transport está indefinido</span><span class="sxs-lookup"><span data-stu-id="3c421-183">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="3c421-184">Erro: não é possível se conectar ao servidor com nenhum dos transportes disponíveis.</span><span class="sxs-lookup"><span data-stu-id="3c421-184">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="3c421-185">Falha no WebSockets</span><span class="sxs-lookup"><span data-stu-id="3c421-185">WebSockets failed</span></span>
* <span data-ttu-id="3c421-186">Erro: não é possível enviar dados se a conexão não estiver no estado "conectado".</span><span class="sxs-lookup"><span data-stu-id="3c421-186">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="3c421-187">Para obter mais informações, consulte a [documentação do Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="3c421-187">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="3c421-188">Medir latência de rede</span><span class="sxs-lookup"><span data-stu-id="3c421-188">Measure network latency</span></span>

<span data-ttu-id="3c421-189">A [interoperabilidade js](xref:blazor/call-javascript-from-dotnet) pode ser usada para medir a latência de rede, como demonstra o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="3c421-189">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code {
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            startTime = DateTime.UtcNow;
            var _ = await JS.InvokeAsync<string>("toString");
            latency = DateTime.UtcNow - startTime;
            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="3c421-190">Para uma experiência de interface do usuário razoável, recomendamos uma latência de interface do usuário sustentada de 250 MS ou menos.</span><span class="sxs-lookup"><span data-stu-id="3c421-190">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
