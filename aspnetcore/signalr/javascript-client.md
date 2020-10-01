---
title: ASP.NET Core SignalR cliente JavaScript
author: bradygaster
description: Visão geral de ASP.NET Core SignalR cliente JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/javascript-client
ms.openlocfilehash: 6fc586d144547585ef75d653bf54193def5c8b7f
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606681"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="8d2b3-103">ASP.NET Core SignalR cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="8d2b3-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8d2b3-104">Por [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="8d2b3-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="8d2b3-105">A SignalR biblioteca de cliente ASP.NET Core JavaScript permite que os desenvolvedores chamem o código de Hub do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="8d2b3-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8d2b3-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="8d2b3-107">Instalar o SignalR pacote do cliente</span><span class="sxs-lookup"><span data-stu-id="8d2b3-107">Install the SignalR client package</span></span>

<span data-ttu-id="8d2b3-108">A SignalR biblioteca de cliente JavaScript é entregue como um pacote [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="8d2b3-109">As seções a seguir descrevem diferentes maneiras de instalar a biblioteca de cliente.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="8d2b3-110">Instalar com NPM</span><span class="sxs-lookup"><span data-stu-id="8d2b3-110">Install with npm</span></span>

<span data-ttu-id="8d2b3-111">Para o Visual Studio, execute os seguintes comandos no **console do Gerenciador de pacotes** enquanto estiver na pasta raiz.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="8d2b3-112">Para Visual Studio Code, execute os seguintes comandos do **terminal integrado**.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="8d2b3-113">NPM instala o conteúdo do pacote na *pasta \\ @microsoft\signalr\dist\browser node_modules* .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="8d2b3-114">Crie uma nova pasta chamada *signalr* na pasta *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="8d2b3-115">Copie o arquivo de *signalr.js* para a pasta *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="8d2b3-116">Referencie o SignalR cliente JavaScript no `<script>` elemento.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="8d2b3-117">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="8d2b3-118">Usar uma CDN (rede de distribuição de conteúdo)</span><span class="sxs-lookup"><span data-stu-id="8d2b3-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="8d2b3-119">Para usar a biblioteca de cliente sem o pré-requisito NPM, faça referência a uma cópia hospedada em CDN da biblioteca de cliente.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="8d2b3-120">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="8d2b3-121">A biblioteca de cliente está disponível no seguinte CDNs:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="8d2b3-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="8d2b3-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="8d2b3-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="8d2b3-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="8d2b3-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="8d2b3-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="8d2b3-125">Instalar com LibMan</span><span class="sxs-lookup"><span data-stu-id="8d2b3-125">Install with LibMan</span></span>

<span data-ttu-id="8d2b3-126">[LibMan](xref:client-side/libman/index) pode ser usado para instalar arquivos de biblioteca de cliente específicos da biblioteca de cliente hospedada na CDN.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="8d2b3-127">Por exemplo, adicione apenas o arquivo JavaScript reduzidos ao projeto.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="8d2b3-128">Para obter detalhes sobre essa abordagem, consulte [Adicionar a SignalR biblioteca de cliente](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="8d2b3-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="8d2b3-129">Conectar-se a um hub</span><span class="sxs-lookup"><span data-stu-id="8d2b3-129">Connect to a hub</span></span>

<span data-ttu-id="8d2b3-130">O código a seguir cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="8d2b3-131">O nome do Hub não diferencia maiúsculas de minúsculas:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="8d2b3-132">Conexões entre origens</span><span class="sxs-lookup"><span data-stu-id="8d2b3-132">Cross-origin connections</span></span>

<span data-ttu-id="8d2b3-133">Normalmente, os navegadores carregam conexões do mesmo domínio que a página solicitada.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="8d2b3-134">No entanto, há ocasiões em que uma conexão com outro domínio é necessária.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="8d2b3-135">Para impedir que um site mal-intencionado leia dados confidenciais de outro site, [as conexões entre origens](xref:security/cors) são desabilitadas por padrão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="8d2b3-136">Para permitir uma solicitação entre origens, habilite-a na `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="8d2b3-137">Métodos do hub de chamadas do cliente</span><span class="sxs-lookup"><span data-stu-id="8d2b3-137">Call hub methods from the client</span></span>

<span data-ttu-id="8d2b3-138">Os clientes JavaScript chamam métodos públicos em hubs por meio do método [Invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) de [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="8d2b3-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="8d2b3-139">O `invoke` método aceita:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="8d2b3-140">O nome do método de Hub.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-140">The name of the hub method.</span></span>
* <span data-ttu-id="8d2b3-141">Quaisquer argumentos definidos no método Hub.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="8d2b3-142">No exemplo a seguir, o nome do método no Hub é `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="8d2b3-143">O segundo e o terceiro argumentos passados para `invoke` mapear para os argumentos e os métodos do Hub `user` `message` :</span><span class="sxs-lookup"><span data-stu-id="8d2b3-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="8d2b3-144">Somente há suporte para métodos de Hub de chamada de um cliente ao usar o serviço do Azure SignalR no modo *padrão* .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="8d2b3-145">Para obter mais informações, consulte perguntas frequentes [(repositório GitHub do Azure-signalr)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="8d2b3-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="8d2b3-146">O `invoke` método retorna uma [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="8d2b3-147">O `Promise` é resolvido com o valor de retorno (se houver) quando o método no servidor retorna.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="8d2b3-148">Se o método no servidor gerar um erro, o `Promise` será rejeitado com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="8d2b3-149">Use `async` e `await` ou os `Promise` `then` métodos e `catch` para lidar com esses casos.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="8d2b3-150">Os clientes JavaScript também podem chamar métodos públicos em hubs por meio do método [Send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) do `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="8d2b3-151">Ao contrário do `invoke` método, o `send` método não aguarda uma resposta do servidor.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="8d2b3-152">O `send` método retorna um JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="8d2b3-153">O `Promise` é resolvido quando a mensagem é enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="8d2b3-154">Se houver um erro ao enviar a mensagem, o `Promise` será rejeitado com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="8d2b3-155">Use `async` e `await` ou os `Promise` `then` métodos e `catch` para lidar com esses casos.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="8d2b3-156">`send`O uso de não aguarda até que o servidor tenha recebido a mensagem.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="8d2b3-157">Consequentemente, não é possível retornar dados ou erros do servidor.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="8d2b3-158">Chamar métodos de cliente do Hub</span><span class="sxs-lookup"><span data-stu-id="8d2b3-158">Call client methods from the hub</span></span>

<span data-ttu-id="8d2b3-159">Para receber mensagens do Hub, defina um método usando o método [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) do `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="8d2b3-160">O nome do método de cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="8d2b3-161">Argumentos que o Hub passa para o método.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="8d2b3-162">No exemplo a seguir, o nome do método é `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="8d2b3-163">Os nomes dos argumentos são `user` e `message` :</span><span class="sxs-lookup"><span data-stu-id="8d2b3-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="8d2b3-164">O código anterior em `connection.on` é executado quando o código do lado do servidor o chama usando o <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> método:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="8d2b3-165">SignalR determina qual método de cliente chamar correspondendo o nome do método e os argumentos definidos em `SendAsync` e `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="8d2b3-166">Como prática recomendada, chame o método [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) no `HubConnection` After `on` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="8d2b3-167">Isso garante que seus manipuladores sejam registrados antes que todas as mensagens sejam recebidas.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="8d2b3-168">Registro em log e tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="8d2b3-168">Error handling and logging</span></span>

<span data-ttu-id="8d2b3-169">Use `try` e `catch` com `async` e `await` ou o `Promise` `catch` método do para manipular erros do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="8d2b3-170">Use `console.error` para gerar erros de saída para o console do navegador:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="8d2b3-171">Configure o rastreamento de log no lado do cliente passando um agente e um tipo de evento para registrar quando a conexão é feita.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="8d2b3-172">As mensagens são registradas com o nível de log especificado e superior.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="8d2b3-173">Os níveis de log disponíveis são os seguintes:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="8d2b3-174">`signalR.LogLevel.Error`: Mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="8d2b3-175">Registra `Error` somente mensagens.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="8d2b3-176">`signalR.LogLevel.Warning`: Mensagens de aviso sobre erros potenciais.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="8d2b3-177">Logs `Warning` e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="8d2b3-178">`signalR.LogLevel.Information`: Mensagens de status sem erros.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="8d2b3-179">Logs `Information` , `Warning` e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="8d2b3-180">`signalR.LogLevel.Trace`: Rastrear mensagens.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="8d2b3-181">Registra tudo, incluindo dados transportados entre o Hub e o cliente.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="8d2b3-182">Use o método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) no [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar o nível de log.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="8d2b3-183">As mensagens são registradas no console do navegador:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="8d2b3-184">Reconectar clientes</span><span class="sxs-lookup"><span data-stu-id="8d2b3-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="8d2b3-185">Reconectar automaticamente</span><span class="sxs-lookup"><span data-stu-id="8d2b3-185">Automatically reconnect</span></span>

<span data-ttu-id="8d2b3-186">O cliente JavaScript para SignalR pode ser configurado para reconectar-se automaticamente usando o `withAutomaticReconnect` método em [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="8d2b3-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="8d2b3-187">Ele não se reconectará automaticamente por padrão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="8d2b3-188">Sem parâmetros, `withAutomaticReconnect()` o configura o cliente para aguardar 0, 2, 10 e 30 segundos, respectivamente, antes de tentar cada tentativa de reconexão, parando após quatro tentativas com falha.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="8d2b3-189">Antes de iniciar qualquer tentativa de reconexão, o `HubConnection` fará a transição para o `HubConnectionState.Reconnecting` estado e acionará seus `onreconnecting` retornos de chamada em vez de fazer a transição para o `Disconnected` estado e disparar seus `onclose` retornos de chamada como um `HubConnection` sem reconexão automática configurada.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="8d2b3-190">Isso fornece uma oportunidade para avisar os usuários de que a conexão foi perdida e para desabilitar os elementos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="8d2b3-191">Se o cliente se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição de volta para o `Connected` estado e acionará seus `onreconnected` retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="8d2b3-192">Isso fornece uma oportunidade de informar aos usuários que a conexão foi restabelecida.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="8d2b3-193">Como a conexão é totalmente nova no servidor, um novo `connectionId` será fornecido para o retorno de `onreconnected` chamada.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="8d2b3-194">O `onreconnected` parâmetro do retorno de chamada `connectionId` será indefinido se o `HubConnection` tiver sido configurado para [ignorar a negociação](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="8d2b3-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="8d2b3-195">`withAutomaticReconnect()` não configurará o `HubConnection` para tentar falhas de início inicial, portanto, as falhas de início precisam ser manipuladas manualmente:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("SignalR Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="8d2b3-196">Se o cliente não se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição para o `Disconnected` estado e acionará seus retornos de chamada [fechamento](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="8d2b3-197">Isso fornece uma oportunidade de informar aos usuários que a conexão foi permanentemente perdida e recomenda atualizar a página:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="8d2b3-198">Para configurar um número personalizado de tentativas de reconexão antes de desconectar ou alterar o tempo de reconexão, o `withAutomaticReconnect` aceita uma matriz de números que representa o atraso em milissegundos para aguardar antes de iniciar cada tentativa de reconexão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="8d2b3-199">O exemplo anterior configura o `HubConnection` para iniciar a tentativa de reconectar imediatamente após a perda da conexão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="8d2b3-200">Isso também é verdadeiro para a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="8d2b3-201">Se a primeira tentativa de reconexão falhar, a segunda tentativa de reconexão também será iniciada imediatamente, em vez de esperar 2 segundos, como seria na configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="8d2b3-202">Se a segunda tentativa de reconexão falhar, a terceira tentativa de reconexão será iniciada em 10 segundos, o que é novamente como a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="8d2b3-203">O comportamento personalizado, em seguida, deriva novamente do comportamento padrão ao parar após a terceira tentativa de reconexão, em vez de tentar mais uma tentativa de reconexão em outros 30 segundos, como seria na configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="8d2b3-204">Se você quiser ainda mais controle sobre o tempo e o número de tentativas de reconexão automática, o `withAutomaticReconnect` aceitará um objeto que implementa a `IRetryPolicy` interface, que tem um único método chamado `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="8d2b3-205">`nextRetryDelayInMilliseconds` usa um único argumento com o tipo `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="8d2b3-206">O `RetryContext` tem três propriedades: `previousRetryCount` , `elapsedMilliseconds` e `retryReason` que são a `number` , a `number` e uma `Error` respectivamente.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="8d2b3-207">Antes da primeira tentativa de reconexão, `previousRetryCount` e `elapsedMilliseconds` será zero, e o `retryReason` será o erro que causou a perda da conexão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="8d2b3-208">Após cada tentativa de repetição com falha, o `previousRetryCount` será incrementado em um, `elapsedMilliseconds` será atualizado para refletir a quantidade de tempo gasto reconectando até agora em milissegundos, e o `retryReason` será o erro que causou a falha da última tentativa de reconexão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="8d2b3-209">`nextRetryDelayInMilliseconds` deve retornar um número que representa o número de milissegundos a aguardar antes da próxima tentativa de reconexão ou `null` se o `HubConnection` deve parar de reconectar.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

<span data-ttu-id="8d2b3-210">Como alternativa, você pode escrever um código que reconectará o cliente manualmente, conforme demonstrado na [reconexão manual](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="8d2b3-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="8d2b3-211">Reconectar manualmente</span><span class="sxs-lookup"><span data-stu-id="8d2b3-211">Manually reconnect</span></span>

<span data-ttu-id="8d2b3-212">O código a seguir demonstra uma abordagem de reconexão manual típica:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="8d2b3-213">Uma função (nesse caso, a `start` função) é criada para iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="8d2b3-214">Chame a `start` função no manipulador de eventos da conexão `onclose` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="8d2b3-215">Uma implementação do mundo real usaria um retirada exponencial ou tentaria um número especificado de vezes antes de desistir.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="troubleshoot-websocket-handshake-errors"></a><span data-ttu-id="8d2b3-216">Solucionar erros de handshake do WebSocket</span><span class="sxs-lookup"><span data-stu-id="8d2b3-216">Troubleshoot WebSocket handshake errors</span></span>

<span data-ttu-id="8d2b3-217">Esta seção fornece ajuda sobre a exceção *"erro durante o handshake do WebSocket"* que ocorre ao tentar estabelecer conexão com o SignalR Hub de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-217">This section provides help on the *"Error during WebSocket handshake"* exception that occurs when trying to establish connection to ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="8d2b3-218">Código de resposta 400 ou 503</span><span class="sxs-lookup"><span data-stu-id="8d2b3-218">Response code 400 or 503</span></span>

<span data-ttu-id="8d2b3-219">Para o seguinte erro:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-219">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="8d2b3-220">O erro geralmente é causado pelo cliente usar apenas o transporte WebSockets, mas o protocolo WebSockets não está habilitado no servidor.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-220">The error is usually caused by client only use WebSockets transport but WebSockets protocol is not enabled on server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="8d2b3-221">Código de resposta 307</span><span class="sxs-lookup"><span data-stu-id="8d2b3-221">Response code 307</span></span>

```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="8d2b3-222">Frequentemente isso ocorre quando o SignalR servidor Hub:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-222">Frequently this occurs when the SignalR hub server:</span></span>

* <span data-ttu-id="8d2b3-223">Escuta e responde através de HTTP e HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-223">Listens to and responds over both HTTP and HTTPS.</span></span>
* <span data-ttu-id="8d2b3-224">É configurado para impor o HTTPS chamando `UseHttpsRedirection` em `Startup` ou impõe HTTPS por meio da regra de regravação de URL.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-224">Is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="8d2b3-225">Esse erro pode ser causado pela especificação da URL HTTP no lado do cliente usando `.withUrl("http://xxx/HubName")` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-225">This error can be caused by specifying the HTTP URL on client side using `.withUrl("http://xxx/HubName")`.</span></span> <span data-ttu-id="8d2b3-226">A correção para esse caso é modificar o código para usar uma URL HTTPS.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-226">The fix for this case is modifying the code to use an HTTPS URL.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="8d2b3-227">Código de resposta 404</span><span class="sxs-lookup"><span data-stu-id="8d2b3-227">Response code 404</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

<span data-ttu-id="8d2b3-228">Se o aplicativo funciona no localhost, mas retorna esse erro após a publicação no servidor IIS:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-228">If the app works on localhost, but returns this error after publishing to IIS server:</span></span>

* <span data-ttu-id="8d2b3-229">Verifique se o SignalR aplicativo ASP.NET Core está hospedado como um subaplicativo do IIS.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-229">Verify the ASP.NET Core SignalR app is hosted as an IIS sub-application.</span></span>
* <span data-ttu-id="8d2b3-230">Não defina a URL com o pathbase do subaplicativo no SignalR lado do cliente JavaScript `.withUrl("/SubAppName/HubName")` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-230">Don't set URL with the sub-app's pathbase on SignalR JavaScript client side `.withUrl("/SubAppName/HubName")`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8d2b3-231">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8d2b3-231">Additional resources</span></span>

* [<span data-ttu-id="8d2b3-232">Referência da API JavaScript</span><span class="sxs-lookup"><span data-stu-id="8d2b3-232">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="8d2b3-233">Tutorial do JavaScript</span><span class="sxs-lookup"><span data-stu-id="8d2b3-233">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="8d2b3-234">Tutorial do webpack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="8d2b3-234">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="8d2b3-235">Hubs</span><span class="sxs-lookup"><span data-stu-id="8d2b3-235">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="8d2b3-236">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="8d2b3-236">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="8d2b3-237">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="8d2b3-237">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="8d2b3-238">Solicitações entre origens (CORS)</span><span class="sxs-lookup"><span data-stu-id="8d2b3-238">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="8d2b3-239">Documentação sem servidor do serviço do Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="8d2b3-239">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8d2b3-240">Por [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="8d2b3-240">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="8d2b3-241">A SignalR biblioteca de cliente ASP.NET Core JavaScript permite que os desenvolvedores chamem o código de Hub do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-241">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="8d2b3-242">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8d2b3-242">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="8d2b3-243">Instalar o SignalR pacote do cliente</span><span class="sxs-lookup"><span data-stu-id="8d2b3-243">Install the SignalR client package</span></span>

<span data-ttu-id="8d2b3-244">A SignalR biblioteca de cliente JavaScript é entregue como um pacote [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-244">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="8d2b3-245">As seções a seguir descrevem diferentes maneiras de instalar a biblioteca de cliente.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-245">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="8d2b3-246">Instalar com NPM</span><span class="sxs-lookup"><span data-stu-id="8d2b3-246">Install with npm</span></span>

<span data-ttu-id="8d2b3-247">Se estiver usando o Visual Studio, execute os seguintes comandos no **console do Gerenciador de pacotes** enquanto estiver na pasta raiz.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-247">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="8d2b3-248">Para Visual Studio Code, execute os seguintes comandos do **terminal integrado**.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-248">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="8d2b3-249">NPM instala o conteúdo do pacote na *pasta \\ @aspnet\signalr\dist\browser node_modules* .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-249">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="8d2b3-250">Crie uma nova pasta chamada *signalr* na pasta *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-250">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="8d2b3-251">Copie o arquivo de *signalr.js* para a pasta *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-251">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="8d2b3-252">Referencie o SignalR cliente JavaScript no `<script>` elemento.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-252">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="8d2b3-253">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-253">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="8d2b3-254">Usar uma CDN (rede de distribuição de conteúdo)</span><span class="sxs-lookup"><span data-stu-id="8d2b3-254">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="8d2b3-255">Para usar a biblioteca de cliente sem o pré-requisito NPM, faça referência a uma cópia hospedada em CDN da biblioteca de cliente.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-255">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="8d2b3-256">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-256">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="8d2b3-257">A biblioteca de cliente está disponível no seguinte CDNs:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-257">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="8d2b3-258">cdnjs</span><span class="sxs-lookup"><span data-stu-id="8d2b3-258">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="8d2b3-259">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="8d2b3-259">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="8d2b3-260">unpkg</span><span class="sxs-lookup"><span data-stu-id="8d2b3-260">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="8d2b3-261">Instalar com LibMan</span><span class="sxs-lookup"><span data-stu-id="8d2b3-261">Install with LibMan</span></span>

<span data-ttu-id="8d2b3-262">[LibMan](xref:client-side/libman/index) pode ser usado para instalar arquivos de biblioteca de cliente específicos da biblioteca de cliente hospedada na CDN.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-262">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="8d2b3-263">Por exemplo, adicione apenas o arquivo JavaScript reduzidos ao projeto.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-263">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="8d2b3-264">Para obter detalhes sobre essa abordagem, consulte [Adicionar a SignalR biblioteca de cliente](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="8d2b3-264">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="8d2b3-265">Conectar-se a um hub</span><span class="sxs-lookup"><span data-stu-id="8d2b3-265">Connect to a hub</span></span>

<span data-ttu-id="8d2b3-266">O código a seguir cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-266">The following code creates and starts a connection.</span></span> <span data-ttu-id="8d2b3-267">O nome do Hub não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-267">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="8d2b3-268">Conexões entre origens</span><span class="sxs-lookup"><span data-stu-id="8d2b3-268">Cross-origin connections</span></span>

<span data-ttu-id="8d2b3-269">Normalmente, os navegadores carregam conexões do mesmo domínio que a página solicitada.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-269">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="8d2b3-270">No entanto, há ocasiões em que uma conexão com outro domínio é necessária.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-270">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="8d2b3-271">Para impedir que um site mal-intencionado leia dados confidenciais de outro site, [as conexões entre origens](xref:security/cors) são desabilitadas por padrão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-271">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="8d2b3-272">Para permitir uma solicitação entre origens, habilite-a na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-272">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="8d2b3-273">Métodos do hub de chamadas do cliente</span><span class="sxs-lookup"><span data-stu-id="8d2b3-273">Call hub methods from client</span></span>

<span data-ttu-id="8d2b3-274">Os clientes JavaScript chamam métodos públicos em hubs por meio do método [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="8d2b3-274">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="8d2b3-275">O `invoke` método aceita dois argumentos:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-275">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="8d2b3-276">O nome do método de Hub.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-276">The name of the hub method.</span></span> <span data-ttu-id="8d2b3-277">No exemplo a seguir, o nome do método no Hub é `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-277">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="8d2b3-278">Quaisquer argumentos definidos no método Hub.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-278">Any arguments defined in the hub method.</span></span> <span data-ttu-id="8d2b3-279">No exemplo a seguir, o nome do argumento é `message` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-279">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="8d2b3-280">O código de exemplo usa a sintaxe de função de seta que tem suporte nas versões atuais de todos os principais navegadores, exceto o Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-280">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="8d2b3-281">Somente há suporte para métodos de Hub de chamada de um cliente ao usar o serviço do Azure SignalR no modo *padrão* .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-281">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="8d2b3-282">Para obter mais informações, consulte perguntas frequentes [(repositório GitHub do Azure-signalr)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="8d2b3-282">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="8d2b3-283">O `invoke` método retorna uma [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-283">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="8d2b3-284">O `Promise` é resolvido com o valor de retorno (se houver) quando o método no servidor retorna.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-284">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="8d2b3-285">Se o método no servidor gerar um erro, o `Promise` será rejeitado com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-285">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="8d2b3-286">Use os `then` `catch` métodos e na `Promise` própria para lidar com esses casos (ou `await` sintaxe).</span><span class="sxs-lookup"><span data-stu-id="8d2b3-286">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="8d2b3-287">O `send` método retorna um JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-287">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="8d2b3-288">O `Promise` é resolvido quando a mensagem é enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-288">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="8d2b3-289">Se houver um erro ao enviar a mensagem, o `Promise` será rejeitado com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-289">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="8d2b3-290">Use os `then` `catch` métodos e na `Promise` própria para lidar com esses casos (ou `await` sintaxe).</span><span class="sxs-lookup"><span data-stu-id="8d2b3-290">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="8d2b3-291">`send`O uso de não aguarda até que o servidor tenha recebido a mensagem.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-291">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="8d2b3-292">Consequentemente, não é possível retornar dados ou erros do servidor.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-292">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="8d2b3-293">Chamar métodos de cliente do Hub</span><span class="sxs-lookup"><span data-stu-id="8d2b3-293">Call client methods from hub</span></span>

<span data-ttu-id="8d2b3-294">Para receber mensagens do Hub, defina um método usando o método [on](/javascript/api/%40aspnet/signalr/hubconnection#on) do `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-294">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="8d2b3-295">O nome do método de cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-295">The name of the JavaScript client method.</span></span> <span data-ttu-id="8d2b3-296">No exemplo a seguir, o nome do método é `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-296">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="8d2b3-297">Argumentos que o Hub passa para o método.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-297">Arguments the hub passes to the method.</span></span> <span data-ttu-id="8d2b3-298">No exemplo a seguir, o valor do argumento é `message` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-298">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="8d2b3-299">O código anterior em `connection.on` é executado quando o código do lado do servidor o chama usando o <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> método.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-299">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="8d2b3-300">SignalR determina qual método de cliente chamar correspondendo o nome do método e os argumentos definidos em `SendAsync` e `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-300">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="8d2b3-301">Como prática recomendada, chame o método [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) no `HubConnection` After `on` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-301">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="8d2b3-302">Isso garante que seus manipuladores sejam registrados antes que todas as mensagens sejam recebidas.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-302">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="8d2b3-303">Registro em log e tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="8d2b3-303">Error handling and logging</span></span>

<span data-ttu-id="8d2b3-304">Encadear um `catch` método ao final do `start` método para manipular erros do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-304">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="8d2b3-305">Use `console.error` para gerar erros de saída para o console do navegador.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-305">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="8d2b3-306">Configure o rastreamento de log no lado do cliente passando um agente e um tipo de evento para registrar quando a conexão é feita.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-306">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="8d2b3-307">As mensagens são registradas com o nível de log especificado e superior.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-307">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="8d2b3-308">Os níveis de log disponíveis são os seguintes:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-308">Available log levels are as follows:</span></span>

* <span data-ttu-id="8d2b3-309">`signalR.LogLevel.Error`: Mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-309">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="8d2b3-310">Registra `Error` somente mensagens.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-310">Logs `Error` messages only.</span></span>
* <span data-ttu-id="8d2b3-311">`signalR.LogLevel.Warning`: Mensagens de aviso sobre erros potenciais.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-311">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="8d2b3-312">Logs `Warning` e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-312">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="8d2b3-313">`signalR.LogLevel.Information`: Mensagens de status sem erros.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-313">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="8d2b3-314">Logs `Information` , `Warning` e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-314">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="8d2b3-315">`signalR.LogLevel.Trace`: Rastrear mensagens.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-315">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="8d2b3-316">Registra tudo, incluindo dados transportados entre o Hub e o cliente.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-316">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="8d2b3-317">Use o método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) no [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar o nível de log.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-317">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="8d2b3-318">As mensagens são registradas no console do navegador.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-318">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="8d2b3-319">Reconectar clientes</span><span class="sxs-lookup"><span data-stu-id="8d2b3-319">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="8d2b3-320">Reconectar manualmente</span><span class="sxs-lookup"><span data-stu-id="8d2b3-320">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="8d2b3-321">Antes de 3,0, o cliente JavaScript para SignalR não se reconectar automaticamente.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-321">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="8d2b3-322">Você deve escrever um código que reconectará o cliente manualmente.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-322">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="8d2b3-323">O código a seguir demonstra uma abordagem de reconexão manual típica:</span><span class="sxs-lookup"><span data-stu-id="8d2b3-323">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="8d2b3-324">Uma função (nesse caso, a `start` função) é criada para iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-324">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="8d2b3-325">Chame a `start` função no manipulador de eventos da conexão `onclose` .</span><span class="sxs-lookup"><span data-stu-id="8d2b3-325">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="8d2b3-326">Uma implementação do mundo real usaria um retirada exponencial ou tentaria um número especificado de vezes antes de desistir.</span><span class="sxs-lookup"><span data-stu-id="8d2b3-326">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8d2b3-327">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8d2b3-327">Additional resources</span></span>

* [<span data-ttu-id="8d2b3-328">Referência da API JavaScript</span><span class="sxs-lookup"><span data-stu-id="8d2b3-328">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="8d2b3-329">Tutorial do JavaScript</span><span class="sxs-lookup"><span data-stu-id="8d2b3-329">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="8d2b3-330">Tutorial do webpack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="8d2b3-330">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="8d2b3-331">Hubs</span><span class="sxs-lookup"><span data-stu-id="8d2b3-331">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="8d2b3-332">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="8d2b3-332">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="8d2b3-333">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="8d2b3-333">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="8d2b3-334">Solicitações entre origens (CORS)</span><span class="sxs-lookup"><span data-stu-id="8d2b3-334">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="8d2b3-335">Documentação sem servidor do serviço do Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="8d2b3-335">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
