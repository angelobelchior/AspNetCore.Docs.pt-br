---
title: ASP.NET Core Blazor configuração do modelo de hospedagem
author: guardrex
description: Saiba mais sobre cenários adicionais para ASP.NET Core Blazor configuração do modelo de hospedagem.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b32710e515d111b7dd6556f1db55082cd56a82b5
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818996"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="4be3d-103">ASP.NET Core Blazor configuração do modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="4be3d-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="4be3d-104">Por [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4be3d-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4be3d-105">Este artigo aborda a configuração do modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="4be3d-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="4be3d-106">SignalRnegociação entre origens para autenticação</span><span class="sxs-lookup"><span data-stu-id="4be3d-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="4be3d-107">*Esta seção aplica-se a Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="4be3d-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="4be3d-108">Para configurar SignalR o cliente subjacente do para enviar credenciais, como cookies ou cabeçalhos de autenticação http:</span><span class="sxs-lookup"><span data-stu-id="4be3d-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="4be3d-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para definir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> as solicitações entre origens [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="4be3d-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="4be3d-110">Atribua o <xref:System.Net.Http.HttpMessageHandler> à <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opção:</span><span class="sxs-lookup"><span data-stu-id="4be3d-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="4be3d-111">Para obter mais informações, consulte <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="4be3d-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="4be3d-112">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="4be3d-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="4be3d-113">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="4be3d-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="4be3d-114">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="4be3d-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="4be3d-115">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="4be3d-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="4be3d-116">Para personalizar a interface do usuário, defina um elemento com um `id` de `components-reconnect-modal` no `<body>` da `_Host.cshtml` Razor página:</span><span class="sxs-lookup"><span data-stu-id="4be3d-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="4be3d-117">Adicione o seguinte à folha de estilos do aplicativo ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="4be3d-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="4be3d-118">A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="4be3d-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="4be3d-119">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="4be3d-119">CSS class</span></span>                       | <span data-ttu-id="4be3d-120">Indicando&hellip;</span><span class="sxs-lookup"><span data-stu-id="4be3d-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="4be3d-121">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="4be3d-121">A lost connection.</span></span> <span data-ttu-id="4be3d-122">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="4be3d-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="4be3d-123">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="4be3d-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="4be3d-124">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="4be3d-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="4be3d-125">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="4be3d-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="4be3d-126">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="4be3d-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="4be3d-127">Para tentar a reconexão, chame `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="4be3d-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="4be3d-128">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="4be3d-128">Reconnection rejected.</span></span> <span data-ttu-id="4be3d-129">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="4be3d-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="4be3d-130">Para recarregar o aplicativo, chame `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="4be3d-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="4be3d-131">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="4be3d-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="4be3d-132">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="4be3d-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="4be3d-133">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="4be3d-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="4be3d-134">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="4be3d-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="4be3d-135">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="4be3d-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="4be3d-136">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="4be3d-136">Render mode</span></span>

<span data-ttu-id="4be3d-137">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="4be3d-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="4be3d-138">Blazor Serveros aplicativos são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="4be3d-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="4be3d-139">Isso é configurado na `_Host.cshtml` Razor página:</span><span class="sxs-lookup"><span data-stu-id="4be3d-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="4be3d-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="4be3d-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="4be3d-141">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="4be3d-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="4be3d-142">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um Blazor aplicativo por meio do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="4be3d-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="4be3d-143">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="4be3d-143">Render mode</span></span> | <span data-ttu-id="4be3d-144">Descrição</span><span class="sxs-lookup"><span data-stu-id="4be3d-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="4be3d-145">Renderiza o componente em HTML estático e inclui um marcador para um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4be3d-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="4be3d-146">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4be3d-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="4be3d-147">Renderiza um marcador para um Blazor Server aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4be3d-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="4be3d-148">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="4be3d-148">Output from the component isn't included.</span></span> <span data-ttu-id="4be3d-149">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um Blazor aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4be3d-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="4be3d-150">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="4be3d-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="4be3d-151">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="4be3d-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="configure-the-no-locsignalr-client-for-no-locblazor-server-apps"></a><span data-ttu-id="4be3d-152">Configurar o SignalR cliente para Blazor Server aplicativos</span><span class="sxs-lookup"><span data-stu-id="4be3d-152">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="4be3d-153">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="4be3d-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="4be3d-154">Configure o SignalR cliente usado por Blazor Server aplicativos no `Pages/_Host.cshtml` arquivo.</span><span class="sxs-lookup"><span data-stu-id="4be3d-154">Configure the SignalR client used by Blazor Server apps in the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="4be3d-155">Coloque um script que chame `Blazor.start` após o `_framework/blazor.server.js` script e dentro da `</body>` marca.</span><span class="sxs-lookup"><span data-stu-id="4be3d-155">Place a script that calls `Blazor.start` after the `_framework/blazor.server.js` script and inside the `</body>` tag.</span></span>

### <a name="logging"></a><span data-ttu-id="4be3d-156">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="4be3d-156">Logging</span></span>

<span data-ttu-id="4be3d-157">Para configurar o SignalR log do cliente:</span><span class="sxs-lookup"><span data-stu-id="4be3d-157">To configure SignalR client logging:</span></span>

* <span data-ttu-id="4be3d-158">Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="4be3d-158">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="4be3d-159">Passe um objeto de configuração ( `configureSignalR` ) que chama `configureLogging` com o nível de log no Client Builder.</span><span class="sxs-lookup"><span data-stu-id="4be3d-159">Pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder.</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="4be3d-160">No exemplo anterior, `information` é equivalente a um nível de log de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="4be3d-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="4be3d-161">Modificar o manipulador de reconexão</span><span class="sxs-lookup"><span data-stu-id="4be3d-161">Modify the reconnection handler</span></span>

<span data-ttu-id="4be3d-162">Os eventos de conexão de circuito do manipulador de reconexão podem ser modificados para comportamentos personalizados, como:</span><span class="sxs-lookup"><span data-stu-id="4be3d-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="4be3d-163">Para notificar o usuário se a conexão for descartada.</span><span class="sxs-lookup"><span data-stu-id="4be3d-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="4be3d-164">Para executar o registro em log (do cliente) quando um circuito é conectado.</span><span class="sxs-lookup"><span data-stu-id="4be3d-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="4be3d-165">Para modificar os eventos de conexão:</span><span class="sxs-lookup"><span data-stu-id="4be3d-165">To modify the connection events:</span></span>

* <span data-ttu-id="4be3d-166">Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="4be3d-166">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="4be3d-167">Registre retornos de chamada para alterações de conexão para conexões descartadas ( `onConnectionDown` ) e conexões estabelecidas/restabelecidas ( `onConnectionUp` ).</span><span class="sxs-lookup"><span data-stu-id="4be3d-167">Register callbacks for connection changes for dropped connections (`onConnectionDown`) and established/re-established connections (`onConnectionUp`).</span></span> <span data-ttu-id="4be3d-168">**Ambos** `onConnectionDown` e `onConnectionUp` deve ser especificado.</span><span class="sxs-lookup"><span data-stu-id="4be3d-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified.</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="4be3d-169">Ajustar a contagem de repetição de reconexão e o intervalo</span><span class="sxs-lookup"><span data-stu-id="4be3d-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="4be3d-170">Para ajustar a contagem de repetição de reconexão e o intervalo:</span><span class="sxs-lookup"><span data-stu-id="4be3d-170">To adjust the reconnection retry count and interval:</span></span>

* <span data-ttu-id="4be3d-171">Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="4be3d-171">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="4be3d-172">Defina o número de repetições ( `maxRetries` ) e o período em milissegundos permitidos para cada tentativa de repetição ( `retryIntervalMilliseconds` ).</span><span class="sxs-lookup"><span data-stu-id="4be3d-172">Set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`).</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

### <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="4be3d-173">Ocultar ou substituir a exibição de reconexão</span><span class="sxs-lookup"><span data-stu-id="4be3d-173">Hide or replace the reconnection display</span></span>

<span data-ttu-id="4be3d-174">Para ocultar a exibição da reconexão:</span><span class="sxs-lookup"><span data-stu-id="4be3d-174">To hide the reconnection display:</span></span>

* <span data-ttu-id="4be3d-175">Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="4be3d-175">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="4be3d-176">Defina o manipulador de reconexão `_reconnectionDisplay` como um objeto vazio ( `{}` ou `new Object()` ).</span><span class="sxs-lookup"><span data-stu-id="4be3d-176">Set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`).</span></span>

```cshtml
    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });
    </script>
</body>
```

<span data-ttu-id="4be3d-177">Para substituir a exibição da reconexão, defina `_reconnectionDisplay` no exemplo anterior como o elemento para exibição:</span><span class="sxs-lookup"><span data-stu-id="4be3d-177">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="4be3d-178">O espaço reservado `{ELEMENT ID}` é a ID do elemento HTML a ser exibido.</span><span class="sxs-lookup"><span data-stu-id="4be3d-178">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="4be3d-179">Influenciar `<head>` elementos de marca HTML</span><span class="sxs-lookup"><span data-stu-id="4be3d-179">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="4be3d-180">*Esta seção aplica-se ao Blazor WebAssembly e ao Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="4be3d-180">*This section applies to Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="4be3d-181">Quando renderizado, `Title` os `Link` componentes, e `Meta` adicionam ou atualizam dados nos `<head>` elementos de marca HTML:</span><span class="sxs-lookup"><span data-stu-id="4be3d-181">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="4be3d-182">No exemplo anterior, espaços reservados para `{TITLE}` , `{URL}` , e `{DESCRIPTION}` são valores de cadeia de caracteres, Razor variáveis ou Razor expressões.</span><span class="sxs-lookup"><span data-stu-id="4be3d-182">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="4be3d-183">As seguintes características se aplicam:</span><span class="sxs-lookup"><span data-stu-id="4be3d-183">The following characteristics apply:</span></span>

* <span data-ttu-id="4be3d-184">Há suporte para o pré-processamento do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="4be3d-184">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="4be3d-185">O `Value` parâmetro é o único parâmetro válido para o `Title` componente.</span><span class="sxs-lookup"><span data-stu-id="4be3d-185">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="4be3d-186">Os atributos HTML fornecidos para `Meta` os `Link` componentes e são capturados em [atributos adicionais](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) e passados para a marca HTML renderizada.</span><span class="sxs-lookup"><span data-stu-id="4be3d-186">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="4be3d-187">Para vários `Title` componentes, o título da página reflete o `Value` do último `Title` componente renderizado.</span><span class="sxs-lookup"><span data-stu-id="4be3d-187">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="4be3d-188">Se vários `Meta` `Link` componentes ou forem incluídos com atributos idênticos, haverá exatamente uma marca HTML renderizada `Meta` por `Link` componente ou.</span><span class="sxs-lookup"><span data-stu-id="4be3d-188">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="4be3d-189">Dois `Meta` ou mais `Link` componentes não podem se referir à mesma marca HTML renderizada.</span><span class="sxs-lookup"><span data-stu-id="4be3d-189">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="4be3d-190">As alterações nos parâmetros dos existentes `Meta` ou dos `Link` componentes são refletidas em suas marcas HTML renderizadas.</span><span class="sxs-lookup"><span data-stu-id="4be3d-190">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="4be3d-191">Quando os `Link` `Meta` componentes ou não são mais renderizados e, portanto, descartados pelo Framework, suas marcas HTML renderizadas são removidas.</span><span class="sxs-lookup"><span data-stu-id="4be3d-191">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="4be3d-192">Quando um dos componentes do Framework é usado em um componente filho, a marca HTML renderizada influencia qualquer outro componente filho do componente pai, desde que o componente filho que contém o componente da estrutura seja renderizado.</span><span class="sxs-lookup"><span data-stu-id="4be3d-192">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="4be3d-193">A distinção entre usar um desses componentes de estrutura em um componente filho e colocar uma marca HTML no `wwwroot/index.html` ou `Pages/_Host.cshtml` é que a marca HTML renderizada de um componente de estrutura:</span><span class="sxs-lookup"><span data-stu-id="4be3d-193">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="4be3d-194">Pode ser modificado pelo estado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4be3d-194">Can be modified by application state.</span></span> <span data-ttu-id="4be3d-195">Uma marca HTML embutida em código não pode ser modificada pelo estado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4be3d-195">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="4be3d-196">É removido do HTML `<head>` quando o componente pai não é mais renderizado.</span><span class="sxs-lookup"><span data-stu-id="4be3d-196">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4be3d-197">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4be3d-197">Additional resources</span></span>

* <xref:fundamentals/logging/index>
