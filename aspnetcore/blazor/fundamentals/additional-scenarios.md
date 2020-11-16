---
title: 'ASP.NET Core Blazor configuração do modelo de hospedagem'
author: guardrex
description: 'Saiba mais sobre cenários adicionais para ASP.NET Core Blazor configuração do modelo de hospedagem.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: ef37c539d377f14a2744c3ead28234d8497df700
ms.sourcegitcommit: e087b6a38e3d38625ebb567a973e75b4d79547b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637672"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="ce59b-103">ASP.NET Core Blazor configuração do modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="ce59b-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="ce59b-104">Por [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ce59b-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ce59b-105">Este artigo aborda a configuração do modelo de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="ce59b-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="ce59b-106">SignalR negociação entre origens para autenticação</span><span class="sxs-lookup"><span data-stu-id="ce59b-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="ce59b-107">*Esta seção aplica-se a Blazor WebAssembly .*</span><span class="sxs-lookup"><span data-stu-id="ce59b-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="ce59b-108">Para configurar SignalR o cliente subjacente do para enviar credenciais, como os cookie cabeçalhos de autenticação s ou http:</span><span class="sxs-lookup"><span data-stu-id="ce59b-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="ce59b-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> para definir <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> as solicitações entre origens [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) :</span><span class="sxs-lookup"><span data-stu-id="ce59b-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="ce59b-110">Atribua o <xref:System.Net.Http.HttpMessageHandler> à <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> opção:</span><span class="sxs-lookup"><span data-stu-id="ce59b-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="ce59b-111">Para obter mais informações, consulte <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="ce59b-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="ce59b-112">Refletir o estado da conexão na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="ce59b-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="ce59b-113">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="ce59b-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ce59b-114">Quando o cliente detecta que a conexão foi perdida, uma interface de usuário padrão é exibida para o usuário enquanto o cliente tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="ce59b-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="ce59b-115">Se a reconexão falhar, o usuário receberá a opção de tentar novamente.</span><span class="sxs-lookup"><span data-stu-id="ce59b-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="ce59b-116">Para personalizar a interface do usuário, defina um elemento com um `id` de `components-reconnect-modal` no `<body>` da `_Host.cshtml` Razor página:</span><span class="sxs-lookup"><span data-stu-id="ce59b-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="ce59b-117">Adicione o seguinte à folha de estilos do aplicativo ( `wwwroot/css/app.css` ou `wwwroot/css/site.css` ):</span><span class="sxs-lookup"><span data-stu-id="ce59b-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="ce59b-118">A tabela a seguir descreve as classes CSS aplicadas ao `components-reconnect-modal` elemento.</span><span class="sxs-lookup"><span data-stu-id="ce59b-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="ce59b-119">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="ce59b-119">CSS class</span></span>                       | <span data-ttu-id="ce59b-120">Indicando&hellip;</span><span class="sxs-lookup"><span data-stu-id="ce59b-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="ce59b-121">Uma conexão perdida.</span><span class="sxs-lookup"><span data-stu-id="ce59b-121">A lost connection.</span></span> <span data-ttu-id="ce59b-122">O cliente está tentando se reconectar.</span><span class="sxs-lookup"><span data-stu-id="ce59b-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="ce59b-123">Mostrar o modal.</span><span class="sxs-lookup"><span data-stu-id="ce59b-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="ce59b-124">Uma conexão ativa é restabelecida com o servidor.</span><span class="sxs-lookup"><span data-stu-id="ce59b-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="ce59b-125">Ocultar a janela restrita.</span><span class="sxs-lookup"><span data-stu-id="ce59b-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="ce59b-126">Falha na reconexão, provavelmente devido a uma falha de rede.</span><span class="sxs-lookup"><span data-stu-id="ce59b-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="ce59b-127">Para tentar a reconexão, chame `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="ce59b-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="ce59b-128">Reconexão rejeitada.</span><span class="sxs-lookup"><span data-stu-id="ce59b-128">Reconnection rejected.</span></span> <span data-ttu-id="ce59b-129">O servidor foi atingido, mas recusou a conexão, e o estado do usuário no servidor foi perdido.</span><span class="sxs-lookup"><span data-stu-id="ce59b-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="ce59b-130">Para recarregar o aplicativo, chame `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="ce59b-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="ce59b-131">Esse estado de conexão pode resultar quando:</span><span class="sxs-lookup"><span data-stu-id="ce59b-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="ce59b-132">Ocorre uma falha no circuito do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="ce59b-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="ce59b-133">O cliente é desconectado por tempo suficiente para o servidor descartar o estado do usuário.</span><span class="sxs-lookup"><span data-stu-id="ce59b-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="ce59b-134">As instâncias dos componentes com os quais o usuário está interagindo são descartadas.</span><span class="sxs-lookup"><span data-stu-id="ce59b-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="ce59b-135">O servidor é reiniciado ou o processo de trabalho do aplicativo é reciclado.</span><span class="sxs-lookup"><span data-stu-id="ce59b-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="ce59b-136">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="ce59b-136">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ce59b-137">*Esta seção se aplica ao Hosted Blazor WebAssembly e ao Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="ce59b-137">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="ce59b-138">Blazor os aplicativos são configurados por padrão para PreRender a interface do usuário no servidor.</span><span class="sxs-lookup"><span data-stu-id="ce59b-138">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="ce59b-139">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="ce59b-139">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="ce59b-140">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="ce59b-140">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ce59b-141">Blazor Server os aplicativos são configurados por padrão para PreRender a interface do usuário no servidor antes que a conexão do cliente com o servidor seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="ce59b-141">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="ce59b-142">Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="ce59b-142">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="ce59b-143">Inicializar o Blazor circuito</span><span class="sxs-lookup"><span data-stu-id="ce59b-143">Initialize the Blazor circuit</span></span>

<span data-ttu-id="ce59b-144">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="ce59b-144">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ce59b-145">Configure o início manual do Blazor Server [ SignalR circuito](xref:blazor/hosting-models#circuits) de um aplicativo no `Pages/_Host.cshtml` arquivo:</span><span class="sxs-lookup"><span data-stu-id="ce59b-145">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="ce59b-146">Adicione um `autostart="false"` atributo à `<script>` marca para o `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="ce59b-146">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="ce59b-147">Coloque um script que chame `Blazor.start` após a `blazor.server.js` marca do script e dentro da marca de fechamento `</body>` .</span><span class="sxs-lookup"><span data-stu-id="ce59b-147">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="ce59b-148">Quando `autostart` o é desabilitado, qualquer aspecto do aplicativo que não dependa do circuito funciona normalmente.</span><span class="sxs-lookup"><span data-stu-id="ce59b-148">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="ce59b-149">Por exemplo, o roteamento do lado do cliente está operacional.</span><span class="sxs-lookup"><span data-stu-id="ce59b-149">For example, client-side routing is operational.</span></span> <span data-ttu-id="ce59b-150">No entanto, qualquer aspecto que dependa do circuito não estará operacional até que `Blazor.start` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="ce59b-150">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="ce59b-151">O comportamento do aplicativo é imprevisível sem um circuito estabelecido.</span><span class="sxs-lookup"><span data-stu-id="ce59b-151">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="ce59b-152">Por exemplo, os métodos de componente não são executados enquanto o circuito é desconectado.</span><span class="sxs-lookup"><span data-stu-id="ce59b-152">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="ce59b-153">Inicializar Blazor quando o documento estiver pronto</span><span class="sxs-lookup"><span data-stu-id="ce59b-153">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="ce59b-154">Para inicializar o Blazor aplicativo quando o documento estiver pronto:</span><span class="sxs-lookup"><span data-stu-id="ce59b-154">To initialize the Blazor app when the document is ready:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="ce59b-155">Cadeia para o `Promise` que resulta de um início manual</span><span class="sxs-lookup"><span data-stu-id="ce59b-155">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="ce59b-156">Para executar tarefas adicionais, como a inicialização de interoperabilidade do JS, use `then` para encadear para o `Promise` que resulta de um Blazor início do aplicativo manual:</span><span class="sxs-lookup"><span data-stu-id="ce59b-156">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="ce59b-157">Configurar o SignalR cliente</span><span class="sxs-lookup"><span data-stu-id="ce59b-157">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="ce59b-158">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="ce59b-158">Logging</span></span>

<span data-ttu-id="ce59b-159">Para configurar SignalR o log do cliente, passe um objeto de configuração ( `configureSignalR` ) que chame `configureLogging` com o nível de log no construtor do cliente:</span><span class="sxs-lookup"><span data-stu-id="ce59b-159">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

```cshtml
<body>

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

<span data-ttu-id="ce59b-160">No exemplo anterior, `information` é equivalente a um nível de log de <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ce59b-160">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="ce59b-161">Modificar o manipulador de reconexão</span><span class="sxs-lookup"><span data-stu-id="ce59b-161">Modify the reconnection handler</span></span>

<span data-ttu-id="ce59b-162">Os eventos de conexão de circuito do manipulador de reconexão podem ser modificados para comportamentos personalizados, como:</span><span class="sxs-lookup"><span data-stu-id="ce59b-162">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="ce59b-163">Para notificar o usuário se a conexão for descartada.</span><span class="sxs-lookup"><span data-stu-id="ce59b-163">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="ce59b-164">Para executar o registro em log (do cliente) quando um circuito é conectado.</span><span class="sxs-lookup"><span data-stu-id="ce59b-164">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="ce59b-165">Para modificar os eventos de conexão, registre os retornos de chamada para as seguintes alterações de conexão:</span><span class="sxs-lookup"><span data-stu-id="ce59b-165">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="ce59b-166">Conexões descartadas usam `onConnectionDown` .</span><span class="sxs-lookup"><span data-stu-id="ce59b-166">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="ce59b-167">Conexões estabelecidas/restabelecidas usam `onConnectionUp` .</span><span class="sxs-lookup"><span data-stu-id="ce59b-167">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="ce59b-168">**Ambos** `onConnectionDown` e `onConnectionUp` deve ser especificado:</span><span class="sxs-lookup"><span data-stu-id="ce59b-168">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="ce59b-169">Ajustar a contagem de repetição de reconexão e o intervalo</span><span class="sxs-lookup"><span data-stu-id="ce59b-169">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="ce59b-170">Para ajustar a contagem de repetição de reconexão e o intervalo, defina o número de repetições ( `maxRetries` ) e o período em milissegundos permitidos para cada tentativa de repetição ( `retryIntervalMilliseconds` ):</span><span class="sxs-lookup"><span data-stu-id="ce59b-170">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

```cshtml
<body>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="ce59b-171">Ocultar ou substituir a exibição de reconexão</span><span class="sxs-lookup"><span data-stu-id="ce59b-171">Hide or replace the reconnection display</span></span>

<span data-ttu-id="ce59b-172">Para ocultar a exibição de reconexão, defina o manipulador de reconexão `_reconnectionDisplay` como um objeto vazio ( `{}` ou `new Object()` ):</span><span class="sxs-lookup"><span data-stu-id="ce59b-172">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

<span data-ttu-id="ce59b-173">Para substituir a exibição da reconexão, defina `_reconnectionDisplay` no exemplo anterior como o elemento para exibição:</span><span class="sxs-lookup"><span data-stu-id="ce59b-173">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="ce59b-174">O espaço reservado `{ELEMENT ID}` é a ID do elemento HTML a ser exibido.</span><span class="sxs-lookup"><span data-stu-id="ce59b-174">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ce59b-175">Personalize o atraso antes da exibição da reconexão aparecer definindo a `transition-delay` propriedade no CSS do aplicativo ( `wwwroot/css/site.css` ) para o elemento modal.</span><span class="sxs-lookup"><span data-stu-id="ce59b-175">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="ce59b-176">O exemplo a seguir define o atraso de transição de 500 MS (padrão) para 1.000 MS (1 segundo):</span><span class="sxs-lookup"><span data-stu-id="ce59b-176">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="ce59b-177">Desconectar o Blazor circuito do cliente</span><span class="sxs-lookup"><span data-stu-id="ce59b-177">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="ce59b-178">Por padrão, um Blazor circuito é desconectado quando o [ `unload` evento da página](https://developer.mozilla.org/docs/Web/API/Window/unload_event) é disparado.</span><span class="sxs-lookup"><span data-stu-id="ce59b-178">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="ce59b-179">Para desconectar o circuito para outros cenários no cliente, invoque `Blazor.disconnect` no manipulador de eventos apropriado.</span><span class="sxs-lookup"><span data-stu-id="ce59b-179">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="ce59b-180">No exemplo a seguir, o circuito é desconectado quando a página está oculta ([ `pagehide` evento](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="ce59b-180">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

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

## <a name="static-files"></a><span data-ttu-id="ce59b-181">Arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="ce59b-181">Static files</span></span>

<span data-ttu-id="ce59b-182">*Esta seção aplica-se a Blazor Server .*</span><span class="sxs-lookup"><span data-stu-id="ce59b-182">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="ce59b-183">Para criar mapeamentos de arquivo adicionais com um <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> ou configurar outros <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> , use **uma** das abordagens a seguir.</span><span class="sxs-lookup"><span data-stu-id="ce59b-183">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="ce59b-184">Nos exemplos a seguir, o `{EXTENSION}` espaço reservado é a extensão de arquivo e o `{CONTENT TYPE}` espaço reservado é o tipo de conteúdo.</span><span class="sxs-lookup"><span data-stu-id="ce59b-184">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="ce59b-185">Configure opções por [injeção de dependência (di)](xref:blazor/fundamentals/dependency-injection) em `Startup.ConfigureServices` ( `Startup.cs` ) usando <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> :</span><span class="sxs-lookup"><span data-stu-id="ce59b-185">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

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

  <span data-ttu-id="ce59b-186">Como essa abordagem configura o mesmo provedor de arquivos usado para servir `blazor.server.js` , certifique-se de que sua configuração personalizada não interfira no fornecimento `blazor.server.js` .</span><span class="sxs-lookup"><span data-stu-id="ce59b-186">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="ce59b-187">Por exemplo, não remova o mapeamento de arquivos JavaScript Configurando o provedor com `provider.Mappings.Remove(".js")` .</span><span class="sxs-lookup"><span data-stu-id="ce59b-187">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="ce59b-188">Use duas chamadas para <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> no `Startup.Configure` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="ce59b-188">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="ce59b-189">Configure o provedor de arquivo personalizado na primeira chamada com <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> .</span><span class="sxs-lookup"><span data-stu-id="ce59b-189">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="ce59b-190">O segundo middleware serve `blazor.server.js` , que usa a configuração de arquivos estáticos padrão fornecida pela Blazor estrutura.</span><span class="sxs-lookup"><span data-stu-id="ce59b-190">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* <span data-ttu-id="ce59b-191">Você pode evitar interferir no serviço `_framework/blazor.server.js` usando <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> o para executar um middleware de arquivo estático personalizado:</span><span class="sxs-lookup"><span data-stu-id="ce59b-191">You can avoid interfering with serving `_framework/blazor.server.js` by using <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> to execute a custom Static File Middleware:</span></span>

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a><span data-ttu-id="ce59b-192">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ce59b-192">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* [<span data-ttu-id="ce59b-193">Blazor Server eventos de reconexão e eventos de ciclo de vida do componente</span><span class="sxs-lookup"><span data-stu-id="ce59b-193">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
