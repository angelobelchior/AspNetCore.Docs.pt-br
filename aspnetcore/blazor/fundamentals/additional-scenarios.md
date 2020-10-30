---
title: 'ASP.NET Core :::no-loc(Blazor)::: configuração do modelo de hospedagem'
author: guardrex
description: 'Saiba mais sobre cenários adicionais para ASP.NET Core :::no-loc(Blazor)::: configuração do modelo de hospedagem.'
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: f8b6e65424948aaa7b28023497bbbf2a1ceb47dd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056043"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="2fffe-103">ASP.NET Core :::no-loc(Blazor)::: configuração do modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="2fffe-103">ASP.NET Core :::no-loc(Blazor)::: hosting model configuration</span></span>

<span data-ttu-id="2fffe-104">Por [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2fffe-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2fffe-105">Este artigo aborda a configuração do modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="2fffe-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="2fffe-106">:::no-loc(SignalR)::: negociação entre origens para autenticação</span><span class="sxs-lookup"><span data-stu-id="2fffe-106">:::no-loc(SignalR)::: cross-origin negotiation for authentication</span></span>

<span data-ttu-id="2fffe-107">*Esta seção aplica-se a :::no-loc(Blazor WebAssembly)::: .*</span><span class="sxs-lookup"><span data-stu-id="2fffe-107">*This section applies to :::no-loc(Blazor WebAssembly):::.*</span></span>

<span data-ttu-id="2fffe-108">Para configurar :::no-loc(SignalR)::: o cliente subjacente do para enviar credenciais, como os :::no-loc(cookie)::: cabeçalhos de autenticação s ou http:</span><span class="sxs-lookup"><span data-stu-id="2fffe-108">To configure :::no-loc(SignalR):::'s underlying client to send credentials, such as :::no-loc(cookie):::s or HTTP authentication headers:</span></span>

* <span data-ttu-id="2fffe-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para definir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> as solicitações entre origens [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="2fffe-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="2fffe-110">Atribua o <xref:System.Net.Http.HttpMessageHandler> à <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opção:</span><span class="sxs-lookup"><span data-stu-id="2fffe-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="2fffe-111">Para obter mais informações, consulte <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="2fffe-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="2fffe-112">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="2fffe-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="2fffe-113">*Esta seção aplica-se a :::no-loc(Blazor Server)::: .*</span><span class="sxs-lookup"><span data-stu-id="2fffe-113">*This section applies to :::no-loc(Blazor Server):::.*</span></span>

<span data-ttu-id="2fffe-114">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="2fffe-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="2fffe-115">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="2fffe-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="2fffe-116">Para personalizar a interface do usuário, defina um elemento com um `id` de `components-reconnect-modal` no `<body>` da `_Host.cshtml` :::no-loc(Razor)::: página:</span><span class="sxs-lookup"><span data-stu-id="2fffe-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` :::no-loc(Razor)::: page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="2fffe-117">Adicione o seguinte à folha de estilos do aplicativo ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="2fffe-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="2fffe-118">A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="2fffe-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="2fffe-119">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="2fffe-119">CSS class</span></span>                       | <span data-ttu-id="2fffe-120">Indicando&hellip;</span><span class="sxs-lookup"><span data-stu-id="2fffe-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="2fffe-121">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="2fffe-121">A lost connection.</span></span> <span data-ttu-id="2fffe-122">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="2fffe-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="2fffe-123">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="2fffe-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="2fffe-124">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="2fffe-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="2fffe-125">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="2fffe-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="2fffe-126">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="2fffe-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="2fffe-127">Para tentar a reconexão, chame `window.:::no-loc(Blazor):::.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="2fffe-127">To attempt reconnection, call `window.:::no-loc(Blazor):::.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="2fffe-128">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="2fffe-128">Reconnection rejected.</span></span> <span data-ttu-id="2fffe-129">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="2fffe-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="2fffe-130">Para recarregar o aplicativo, chame `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="2fffe-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="2fffe-131">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="2fffe-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="2fffe-132">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="2fffe-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="2fffe-133">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="2fffe-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="2fffe-134">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="2fffe-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="2fffe-135">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="2fffe-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="2fffe-136">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="2fffe-136">Render mode</span></span>

<span data-ttu-id="2fffe-137">*Esta seção aplica-se a :::no-loc(Blazor Server)::: .*</span><span class="sxs-lookup"><span data-stu-id="2fffe-137">*This section applies to :::no-loc(Blazor Server):::.*</span></span>

<span data-ttu-id="2fffe-138">:::no-loc(Blazor Server)::: os aplicativos são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="2fffe-138">:::no-loc(Blazor Server)::: apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="2fffe-139">Isso é configurado na `_Host.cshtml` :::no-loc(Razor)::: página:</span><span class="sxs-lookup"><span data-stu-id="2fffe-139">This is set up in the `_Host.cshtml` :::no-loc(Razor)::: page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="2fffe-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="2fffe-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="2fffe-141">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="2fffe-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="2fffe-142">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um :::no-loc(Blazor)::: aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="2fffe-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

| <span data-ttu-id="2fffe-143">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="2fffe-143">Render mode</span></span> | <span data-ttu-id="2fffe-144">Descrição</span><span class="sxs-lookup"><span data-stu-id="2fffe-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="2fffe-145">Renderiza o componente em HTML estático e inclui um marcador para um :::no-loc(Blazor Server)::: aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fffe-145">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="2fffe-146">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um :::no-loc(Blazor)::: aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fffe-146">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="2fffe-147">Renderiza um marcador para um :::no-loc(Blazor Server)::: aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fffe-147">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="2fffe-148">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="2fffe-148">Output from the component isn't included.</span></span> <span data-ttu-id="2fffe-149">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um :::no-loc(Blazor)::: aplicativo.</span><span class="sxs-lookup"><span data-stu-id="2fffe-149">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="2fffe-150">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="2fffe-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="2fffe-151">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="2fffe-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="2fffe-152">Inicializar o :::no-loc(Blazor)::: circuito</span><span class="sxs-lookup"><span data-stu-id="2fffe-152">Initialize the :::no-loc(Blazor)::: circuit</span></span>

<span data-ttu-id="2fffe-153">*Esta seção aplica-se a :::no-loc(Blazor Server)::: .*</span><span class="sxs-lookup"><span data-stu-id="2fffe-153">*This section applies to :::no-loc(Blazor Server):::.*</span></span>

<span data-ttu-id="2fffe-154">Configure o início manual do :::no-loc(Blazor Server)::: [ :::no-loc(SignalR)::: circuito](xref:blazor/hosting-models#circuits) de um aplicativo no `Pages/_Host.cshtml` arquivo:</span><span class="sxs-lookup"><span data-stu-id="2fffe-154">Configure the manual start of a :::no-loc(Blazor Server)::: app's [:::no-loc(SignalR)::: circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="2fffe-155">Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="2fffe-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="2fffe-156">Coloque um script que chame `:::no-loc(Blazor):::.start` após a `blazor.server.js` marca do script e dentro da marca de fechamento `</body>` .</span><span class="sxs-lookup"><span data-stu-id="2fffe-156">Place a script that calls `:::no-loc(Blazor):::.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="2fffe-157">Quando `autostart` o é desabilitado, qualquer aspecto do aplicativo que não dependa do circuito funciona normalmente.</span><span class="sxs-lookup"><span data-stu-id="2fffe-157">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="2fffe-158">Por exemplo, o roteamento do lado do cliente está operacional.</span><span class="sxs-lookup"><span data-stu-id="2fffe-158">For example, client-side routing is operational.</span></span> <span data-ttu-id="2fffe-159">No entanto, qualquer aspecto que dependa do circuito não estará operacional até que `:::no-loc(Blazor):::.start` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="2fffe-159">However, any aspect that depends on the circuit isn't operational until `:::no-loc(Blazor):::.start` is called.</span></span> <span data-ttu-id="2fffe-160">O comportamento do aplicativo é imprevisível sem um circuito estabelecido.</span><span class="sxs-lookup"><span data-stu-id="2fffe-160">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="2fffe-161">Por exemplo, os métodos de componente não são executados enquanto o circuito é desconectado.</span><span class="sxs-lookup"><span data-stu-id="2fffe-161">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="2fffe-162">Inicializar :::no-loc(Blazor)::: quando o documento estiver pronto</span><span class="sxs-lookup"><span data-stu-id="2fffe-162">Initialize :::no-loc(Blazor)::: when the document is ready</span></span>

<span data-ttu-id="2fffe-163">Para inicializar o :::no-loc(Blazor)::: aplicativo quando o documento estiver pronto:</span><span class="sxs-lookup"><span data-stu-id="2fffe-163">To initialize the :::no-loc(Blazor)::: app when the document is ready:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        :::no-loc(Blazor):::.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="2fffe-164">Cadeia para o `Promise` que resulta de um início manual</span><span class="sxs-lookup"><span data-stu-id="2fffe-164">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="2fffe-165">Para executar tarefas adicionais, como a inicialização de interoperabilidade do JS, use `then` para encadear para o `Promise` que resulta de um :::no-loc(Blazor)::: início do aplicativo manual:</span><span class="sxs-lookup"><span data-stu-id="2fffe-165">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual :::no-loc(Blazor)::: app start:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      :::no-loc(Blazor):::.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="2fffe-166">Configurar o :::no-loc(SignalR)::: cliente</span><span class="sxs-lookup"><span data-stu-id="2fffe-166">Configure the :::no-loc(SignalR)::: client</span></span>

#### <a name="logging"></a><span data-ttu-id="2fffe-167">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="2fffe-167">Logging</span></span>

<span data-ttu-id="2fffe-168">Para configurar :::no-loc(SignalR)::: o log do cliente, passe um objeto de configuração ( `configure:::no-loc(SignalR):::` ) que chame `configureLogging` com o nível de log no construtor do cliente:</span><span class="sxs-lookup"><span data-stu-id="2fffe-168">To configure :::no-loc(SignalR)::: client logging, pass in a configuration object (`configure:::no-loc(SignalR):::`) that calls `configureLogging` with the log level on the client builder:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      :::no-loc(Blazor):::.start({
        configure:::no-loc(SignalR):::: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="2fffe-169">No exemplo anterior, `information` é equivalente a um nível de log de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="2fffe-169">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="2fffe-170">Modificar o manipulador de reconexão</span><span class="sxs-lookup"><span data-stu-id="2fffe-170">Modify the reconnection handler</span></span>

<span data-ttu-id="2fffe-171">Os eventos de conexão de circuito do manipulador de reconexão podem ser modificados para comportamentos personalizados, como:</span><span class="sxs-lookup"><span data-stu-id="2fffe-171">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="2fffe-172">Para notificar o usuário se a conexão for descartada.</span><span class="sxs-lookup"><span data-stu-id="2fffe-172">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="2fffe-173">Para executar o registro em log (do cliente) quando um circuito é conectado.</span><span class="sxs-lookup"><span data-stu-id="2fffe-173">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="2fffe-174">Para modificar os eventos de conexão, registre os retornos de chamada para as seguintes alterações de conexão:</span><span class="sxs-lookup"><span data-stu-id="2fffe-174">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="2fffe-175">Conexões descartadas usam `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="2fffe-175">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="2fffe-176">Conexões estabelecidas/restabelecidas usam `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="2fffe-176">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="2fffe-177">**Ambos** `onConnectionDown` e `onConnectionUp` deve ser especificado:</span><span class="sxs-lookup"><span data-stu-id="2fffe-177">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      :::no-loc(Blazor):::.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="2fffe-178">Ajustar a contagem de repetição de reconexão e o intervalo</span><span class="sxs-lookup"><span data-stu-id="2fffe-178">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="2fffe-179">Para ajustar a contagem de repetição de reconexão e o intervalo, defina o número de repetições ( `maxRetries` ) e o período em milissegundos permitidos para cada tentativa de repetição ( `retryIntervalMilliseconds` ):</span><span class="sxs-lookup"><span data-stu-id="2fffe-179">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      :::no-loc(Blazor):::.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="2fffe-180">Ocultar ou substituir a exibição de reconexão</span><span class="sxs-lookup"><span data-stu-id="2fffe-180">Hide or replace the reconnection display</span></span>

<span data-ttu-id="2fffe-181">Para ocultar a exibição de reconexão, defina o manipulador de reconexão `_reconnectionDisplay` como um objeto vazio ( `{}` ou `new Object()` ):</span><span class="sxs-lookup"><span data-stu-id="2fffe-181">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        :::no-loc(Blazor):::.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      :::no-loc(Blazor):::.start();
    </script>
</body>
```

<span data-ttu-id="2fffe-182">Para substituir a exibição da reconexão, defina `_reconnectionDisplay` no exemplo anterior como o elemento para exibição:</span><span class="sxs-lookup"><span data-stu-id="2fffe-182">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
:::no-loc(Blazor):::.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="2fffe-183">O espaço reservado `{ELEMENT ID}` é a ID do elemento HTML a ser exibido.</span><span class="sxs-lookup"><span data-stu-id="2fffe-183">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2fffe-184">Personalize o atraso antes da exibição da reconexão aparecer definindo a `transition-delay` propriedade no CSS do aplicativo ( `wwwroot/css/site.css` ) para o elemento modal.</span><span class="sxs-lookup"><span data-stu-id="2fffe-184">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="2fffe-185">O exemplo a seguir define o atraso de transição de 500 MS (padrão) para 1.000 MS (1 segundo):</span><span class="sxs-lookup"><span data-stu-id="2fffe-185">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="2fffe-186">Desconectar o :::no-loc(Blazor)::: circuito do cliente</span><span class="sxs-lookup"><span data-stu-id="2fffe-186">Disconnect the :::no-loc(Blazor)::: circuit from the client</span></span>

<span data-ttu-id="2fffe-187">Por padrão, um :::no-loc(Blazor)::: circuito é desconectado quando o [ `unload` evento da página](https://developer.mozilla.org/docs/Web/API/Window/unload_event) é disparado.</span><span class="sxs-lookup"><span data-stu-id="2fffe-187">By default, a :::no-loc(Blazor)::: circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="2fffe-188">Para desconectar o circuito para outros cenários no cliente, invoque `:::no-loc(Blazor):::.disconnect` no manipulador de eventos apropriado.</span><span class="sxs-lookup"><span data-stu-id="2fffe-188">To disconnect the circuit for other scenarios on the client, invoke `:::no-loc(Blazor):::.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="2fffe-189">No exemplo a seguir, o circuito é desconectado quando a página está oculta ([ `pagehide` evento](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="2fffe-189">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  :::no-loc(Blazor):::.disconnect();
});
```

<!-- HOLD for reactivation at 5x

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server):::.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, :::no-loc(Razor)::: variables, or :::no-loc(Razor)::: expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="static-files"></a><span data-ttu-id="2fffe-190">Arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="2fffe-190">Static files</span></span>

<span data-ttu-id="2fffe-191">*Esta seção aplica-se a :::no-loc(Blazor Server)::: .*</span><span class="sxs-lookup"><span data-stu-id="2fffe-191">*This section applies to :::no-loc(Blazor Server):::.*</span></span>

<span data-ttu-id="2fffe-192">Para criar mapeamentos de arquivo adicionais com um <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> ou configurar outros <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> , use **uma** das abordagens a seguir.</span><span class="sxs-lookup"><span data-stu-id="2fffe-192">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="2fffe-193">Nos exemplos a seguir, o `{EXTENSION}` espaço reservado é a extensão de arquivo e o `{CONTENT TYPE}` espaço reservado é o tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="2fffe-193">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="2fffe-194">Configure opções por [injeção de dependência (di)](xref:blazor/fundamentals/dependency-injection) em `Startup.ConfigureServices` ( `Startup.cs` ) usando <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :</span><span class="sxs-lookup"><span data-stu-id="2fffe-194">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  <span data-ttu-id="2fffe-195">Como essa abordagem configura o mesmo provedor de arquivos usado para servir `blazor.server.js` , certifique-se de que sua configuração personalizada não interfira no fornecimento `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="2fffe-195">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="2fffe-196">Por exemplo, não remova o mapeamento de arquivos JavaScript Configurando o provedor com `provider.Mappings.Remove(".js")` .</span><span class="sxs-lookup"><span data-stu-id="2fffe-196">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="2fffe-197">Use duas chamadas para <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> no `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="2fffe-197">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="2fffe-198">Configure o provedor de arquivo personalizado na primeira chamada com <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .</span><span class="sxs-lookup"><span data-stu-id="2fffe-198">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="2fffe-199">O segundo middleware serve `blazor.server.js` , que usa a configuração de arquivos estáticos padrão fornecida pela :::no-loc(Blazor)::: estrutura.</span><span class="sxs-lookup"><span data-stu-id="2fffe-199">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the :::no-loc(Blazor)::: framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="2fffe-200">Você pode evitar interferir no serviço `_framework/blazor.server.js` usando <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> o para executar um middleware de arquivo estático personalizado:</span><span class="sxs-lookup"><span data-stu-id="2fffe-200">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a><span data-ttu-id="2fffe-201">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2fffe-201">Additional resources</span></span>

* <xref:fundamentals/logging/index>
