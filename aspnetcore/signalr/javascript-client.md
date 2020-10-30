---
title: 'ASP.NET Core :::no-loc(SignalR)::: cliente JavaScript'
author: bradygaster
description: 'Visão geral de ASP.NET Core :::no-loc(SignalR)::: cliente JavaScript.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 04/08/2020
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
uid: signalr/javascript-client
ms.openlocfilehash: b4b1bc6131a6676710adbf2503efe3f304d89a58
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050843"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="0b77d-103">ASP.NET Core :::no-loc(SignalR)::: cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b77d-103">ASP.NET Core :::no-loc(SignalR)::: JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0b77d-104">Por [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="0b77d-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="0b77d-105">A :::no-loc(SignalR)::: biblioteca de cliente ASP.NET Core JavaScript permite que os desenvolvedores chamem o código de Hub do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="0b77d-105">The ASP.NET Core :::no-loc(SignalR)::: JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="0b77d-106">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0b77d-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="0b77d-107">Instalar o :::no-loc(SignalR)::: pacote do cliente</span><span class="sxs-lookup"><span data-stu-id="0b77d-107">Install the :::no-loc(SignalR)::: client package</span></span>

<span data-ttu-id="0b77d-108">A :::no-loc(SignalR)::: biblioteca de cliente JavaScript é entregue como um pacote [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="0b77d-108">The :::no-loc(SignalR)::: JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="0b77d-109">As seções a seguir descrevem diferentes maneiras de instalar a biblioteca de cliente.</span><span class="sxs-lookup"><span data-stu-id="0b77d-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="0b77d-110">Instalar com NPM</span><span class="sxs-lookup"><span data-stu-id="0b77d-110">Install with npm</span></span>

<span data-ttu-id="0b77d-111">Para o Visual Studio, execute os seguintes comandos no **console do Gerenciador de pacotes** enquanto estiver na pasta raiz.</span><span class="sxs-lookup"><span data-stu-id="0b77d-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="0b77d-112">Para Visual Studio Code, execute os seguintes comandos do **terminal integrado** .</span><span class="sxs-lookup"><span data-stu-id="0b77d-112">For Visual Studio Code, run the following commands from the **Integrated Terminal** .</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="0b77d-113">NPM instala o conteúdo do pacote na *pasta \\ @microsoft\signalr\dist\browser node_modules* .</span><span class="sxs-lookup"><span data-stu-id="0b77d-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="0b77d-114">Crie uma nova pasta chamada *signalr* na pasta *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="0b77d-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="0b77d-115">Copie o arquivo de *signalr.js* para a pasta *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="0b77d-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="0b77d-116">Referencie o :::no-loc(SignalR)::: cliente JavaScript no `<script>` elemento.</span><span class="sxs-lookup"><span data-stu-id="0b77d-116">Reference the :::no-loc(SignalR)::: JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="0b77d-117">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0b77d-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="0b77d-118">Usar uma CDN (rede de distribuição de conteúdo)</span><span class="sxs-lookup"><span data-stu-id="0b77d-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="0b77d-119">Para usar a biblioteca de cliente sem o pré-requisito NPM, faça referência a uma cópia hospedada em CDN da biblioteca de cliente.</span><span class="sxs-lookup"><span data-stu-id="0b77d-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="0b77d-120">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0b77d-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="0b77d-121">A biblioteca de cliente está disponível no seguinte CDNs:</span><span class="sxs-lookup"><span data-stu-id="0b77d-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="0b77d-122">cdnjs</span><span class="sxs-lookup"><span data-stu-id="0b77d-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="0b77d-123">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="0b77d-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="0b77d-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="0b77d-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="0b77d-125">Instalar com LibMan</span><span class="sxs-lookup"><span data-stu-id="0b77d-125">Install with LibMan</span></span>

<span data-ttu-id="0b77d-126">[LibMan](xref:client-side/libman/index) pode ser usado para instalar arquivos de biblioteca de cliente específicos da biblioteca de cliente hospedada na CDN.</span><span class="sxs-lookup"><span data-stu-id="0b77d-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="0b77d-127">Por exemplo, adicione apenas o arquivo JavaScript reduzidos ao projeto.</span><span class="sxs-lookup"><span data-stu-id="0b77d-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="0b77d-128">Para obter detalhes sobre essa abordagem, consulte [Adicionar a :::no-loc(SignalR)::: biblioteca de cliente](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="0b77d-128">For details on that approach, see [Add the :::no-loc(SignalR)::: client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="0b77d-129">Conectar-se a um hub</span><span class="sxs-lookup"><span data-stu-id="0b77d-129">Connect to a hub</span></span>

<span data-ttu-id="0b77d-130">O código a seguir cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="0b77d-131">O nome do Hub não diferencia maiúsculas de minúsculas:</span><span class="sxs-lookup"><span data-stu-id="0b77d-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="0b77d-132">Conexões entre origens</span><span class="sxs-lookup"><span data-stu-id="0b77d-132">Cross-origin connections</span></span>

<span data-ttu-id="0b77d-133">Normalmente, os navegadores carregam conexões do mesmo domínio que a página solicitada.</span><span class="sxs-lookup"><span data-stu-id="0b77d-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="0b77d-134">No entanto, há ocasiões em que uma conexão com outro domínio é necessária.</span><span class="sxs-lookup"><span data-stu-id="0b77d-134">However, there are occasions when a connection to another domain is required.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0b77d-135">O código do cliente deve usar uma URL absoluta em vez de uma URL relativa.</span><span class="sxs-lookup"><span data-stu-id="0b77d-135">The client code must use an absolute URL instead of a relative URL.</span></span> <span data-ttu-id="0b77d-136">Alterar `.withUrl("/chathub")` para `.withUrl("https://myappurl/chathub")`.</span><span class="sxs-lookup"><span data-stu-id="0b77d-136">Change `.withUrl("/chathub")` to `.withUrl("https://myappurl/chathub")`.</span></span>

<span data-ttu-id="0b77d-137">Para impedir que um site mal-intencionado leia dados confidenciais de outro site, [as conexões entre origens](xref:security/cors) são desabilitadas por padrão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-137">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="0b77d-138">Para permitir uma solicitação entre origens, habilite-a na `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="0b77d-138">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="0b77d-139">Métodos do hub de chamadas do cliente</span><span class="sxs-lookup"><span data-stu-id="0b77d-139">Call hub methods from the client</span></span>

<span data-ttu-id="0b77d-140">Os clientes JavaScript chamam métodos públicos em hubs por meio do método [Invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) de [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="0b77d-140">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="0b77d-141">O `invoke` método aceita:</span><span class="sxs-lookup"><span data-stu-id="0b77d-141">The `invoke` method accepts:</span></span>

* <span data-ttu-id="0b77d-142">O nome do método de Hub.</span><span class="sxs-lookup"><span data-stu-id="0b77d-142">The name of the hub method.</span></span>
* <span data-ttu-id="0b77d-143">Quaisquer argumentos definidos no método Hub.</span><span class="sxs-lookup"><span data-stu-id="0b77d-143">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="0b77d-144">No exemplo a seguir, o nome do método no Hub é `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-144">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="0b77d-145">O segundo e o terceiro argumentos passados para `invoke` mapear para os argumentos e os métodos do Hub `user` `message` :</span><span class="sxs-lookup"><span data-stu-id="0b77d-145">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="0b77d-146">Somente há suporte para métodos de Hub de chamada de um cliente ao usar o serviço do Azure :::no-loc(SignalR)::: no modo *padrão* .</span><span class="sxs-lookup"><span data-stu-id="0b77d-146">Calling hub methods from a client is only supported when using the Azure :::no-loc(SignalR)::: Service in *Default* mode.</span></span> <span data-ttu-id="0b77d-147">Para obter mais informações, consulte perguntas frequentes [(repositório GitHub do Azure-signalr)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="0b77d-147">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="0b77d-148">O `invoke` método retorna uma [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0b77d-148">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="0b77d-149">O `Promise` é resolvido com o valor de retorno (se houver) quando o método no servidor retorna.</span><span class="sxs-lookup"><span data-stu-id="0b77d-149">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="0b77d-150">Se o método no servidor gerar um erro, o `Promise` será rejeitado com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="0b77d-150">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="0b77d-151">Use `async` e `await` ou os `Promise` `then` métodos e `catch` para lidar com esses casos.</span><span class="sxs-lookup"><span data-stu-id="0b77d-151">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="0b77d-152">Os clientes JavaScript também podem chamar métodos públicos em hubs por meio do método [Send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) do `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-152">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="0b77d-153">Ao contrário do `invoke` método, o `send` método não aguarda uma resposta do servidor.</span><span class="sxs-lookup"><span data-stu-id="0b77d-153">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="0b77d-154">O `send` método retorna um JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-154">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="0b77d-155">O `Promise` é resolvido quando a mensagem é enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="0b77d-155">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="0b77d-156">Se houver um erro ao enviar a mensagem, o `Promise` será rejeitado com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="0b77d-156">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="0b77d-157">Use `async` e `await` ou os `Promise` `then` métodos e `catch` para lidar com esses casos.</span><span class="sxs-lookup"><span data-stu-id="0b77d-157">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="0b77d-158">`send`O uso de não aguarda até que o servidor tenha recebido a mensagem.</span><span class="sxs-lookup"><span data-stu-id="0b77d-158">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="0b77d-159">Consequentemente, não é possível retornar dados ou erros do servidor.</span><span class="sxs-lookup"><span data-stu-id="0b77d-159">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="0b77d-160">Chamar métodos de cliente do Hub</span><span class="sxs-lookup"><span data-stu-id="0b77d-160">Call client methods from the hub</span></span>

<span data-ttu-id="0b77d-161">Para receber mensagens do Hub, defina um método usando o método [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) do `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-161">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="0b77d-162">O nome do método de cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0b77d-162">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="0b77d-163">Argumentos que o Hub passa para o método.</span><span class="sxs-lookup"><span data-stu-id="0b77d-163">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="0b77d-164">No exemplo a seguir, o nome do método é `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-164">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="0b77d-165">Os nomes dos argumentos são `user` e `message` :</span><span class="sxs-lookup"><span data-stu-id="0b77d-165">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="0b77d-166">O código anterior em `connection.on` é executado quando o código do lado do servidor o chama usando o <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> método:</span><span class="sxs-lookup"><span data-stu-id="0b77d-166">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="0b77d-167">:::no-loc(SignalR)::: determina qual método de cliente chamar correspondendo o nome do método e os argumentos definidos em `SendAsync` e `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-167">:::no-loc(SignalR)::: determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="0b77d-168">Como prática recomendada, chame o método [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) no `HubConnection` After `on` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-168">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="0b77d-169">Isso garante que seus manipuladores sejam registrados antes que todas as mensagens sejam recebidas.</span><span class="sxs-lookup"><span data-stu-id="0b77d-169">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="0b77d-170">Registro em log e tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="0b77d-170">Error handling and logging</span></span>

<span data-ttu-id="0b77d-171">Use `try` e `catch` com `async` e `await` ou o `Promise` `catch` método do para manipular erros do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="0b77d-171">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="0b77d-172">Use `console.error` para gerar erros de saída para o console do navegador:</span><span class="sxs-lookup"><span data-stu-id="0b77d-172">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="0b77d-173">Configure o rastreamento de log no lado do cliente passando um agente e um tipo de evento para registrar quando a conexão é feita.</span><span class="sxs-lookup"><span data-stu-id="0b77d-173">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="0b77d-174">As mensagens são registradas com o nível de log especificado e superior.</span><span class="sxs-lookup"><span data-stu-id="0b77d-174">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="0b77d-175">Os níveis de log disponíveis são os seguintes:</span><span class="sxs-lookup"><span data-stu-id="0b77d-175">Available log levels are as follows:</span></span>

* <span data-ttu-id="0b77d-176">`signalR.LogLevel.Error`: Mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="0b77d-176">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="0b77d-177">Registra `Error` somente mensagens.</span><span class="sxs-lookup"><span data-stu-id="0b77d-177">Logs `Error` messages only.</span></span>
* <span data-ttu-id="0b77d-178">`signalR.LogLevel.Warning`: Mensagens de aviso sobre erros potenciais.</span><span class="sxs-lookup"><span data-stu-id="0b77d-178">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="0b77d-179">Logs `Warning` e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="0b77d-179">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="0b77d-180">`signalR.LogLevel.Information`: Mensagens de status sem erros.</span><span class="sxs-lookup"><span data-stu-id="0b77d-180">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="0b77d-181">Logs `Information` , `Warning` e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="0b77d-181">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="0b77d-182">`signalR.LogLevel.Trace`: Rastrear mensagens.</span><span class="sxs-lookup"><span data-stu-id="0b77d-182">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="0b77d-183">Registra tudo, incluindo dados transportados entre o Hub e o cliente.</span><span class="sxs-lookup"><span data-stu-id="0b77d-183">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="0b77d-184">Use o método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) no [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar o nível de log.</span><span class="sxs-lookup"><span data-stu-id="0b77d-184">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="0b77d-185">As mensagens são registradas no console do navegador:</span><span class="sxs-lookup"><span data-stu-id="0b77d-185">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="0b77d-186">Reconectar clientes</span><span class="sxs-lookup"><span data-stu-id="0b77d-186">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="0b77d-187">Reconectar automaticamente</span><span class="sxs-lookup"><span data-stu-id="0b77d-187">Automatically reconnect</span></span>

<span data-ttu-id="0b77d-188">O cliente JavaScript para :::no-loc(SignalR)::: pode ser configurado para reconectar-se automaticamente usando o `withAutomaticReconnect` método em [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="0b77d-188">The JavaScript client for :::no-loc(SignalR)::: can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="0b77d-189">Ele não se reconectará automaticamente por padrão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-189">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="0b77d-190">Sem parâmetros, `withAutomaticReconnect()` o configura o cliente para aguardar 0, 2, 10 e 30 segundos, respectivamente, antes de tentar cada tentativa de reconexão, parando após quatro tentativas com falha.</span><span class="sxs-lookup"><span data-stu-id="0b77d-190">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="0b77d-191">Antes de iniciar qualquer tentativa de reconexão, o `HubConnection` fará a transição para o `HubConnectionState.Reconnecting` estado e acionará seus `onreconnecting` retornos de chamada em vez de fazer a transição para o `Disconnected` estado e disparar seus `onclose` retornos de chamada como um `HubConnection` sem reconexão automática configurada.</span><span class="sxs-lookup"><span data-stu-id="0b77d-191">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="0b77d-192">Isso fornece uma oportunidade para avisar os usuários de que a conexão foi perdida e para desabilitar os elementos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="0b77d-192">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="0b77d-193">Se o cliente se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição de volta para o `Connected` estado e acionará seus `onreconnected` retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="0b77d-193">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="0b77d-194">Isso fornece uma oportunidade de informar aos usuários que a conexão foi restabelecida.</span><span class="sxs-lookup"><span data-stu-id="0b77d-194">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="0b77d-195">Como a conexão é totalmente nova no servidor, um novo `connectionId` será fornecido para o retorno de `onreconnected` chamada.</span><span class="sxs-lookup"><span data-stu-id="0b77d-195">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="0b77d-196">O `onreconnected` parâmetro do retorno de chamada `connectionId` será indefinido se o `HubConnection` tiver sido configurado para [ignorar a negociação](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="0b77d-196">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="0b77d-197">`withAutomaticReconnect()` não configurará o `HubConnection` para tentar falhas de início inicial, portanto, as falhas de início precisam ser manipuladas manualmente:</span><span class="sxs-lookup"><span data-stu-id="0b77d-197">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log(":::no-loc(SignalR)::: Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="0b77d-198">Se o cliente não se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição para o `Disconnected` estado e acionará seus retornos de chamada [fechamento](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="0b77d-198">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="0b77d-199">Isso fornece uma oportunidade de informar aos usuários que a conexão foi permanentemente perdida e recomenda atualizar a página:</span><span class="sxs-lookup"><span data-stu-id="0b77d-199">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="0b77d-200">Para configurar um número personalizado de tentativas de reconexão antes de desconectar ou alterar o tempo de reconexão, o `withAutomaticReconnect` aceita uma matriz de números que representa o atraso em milissegundos para aguardar antes de iniciar cada tentativa de reconexão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-200">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="0b77d-201">O exemplo anterior configura o `HubConnection` para iniciar a tentativa de reconectar imediatamente após a perda da conexão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-201">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="0b77d-202">Isso também é verdadeiro para a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-202">This is also true for the default configuration.</span></span>

<span data-ttu-id="0b77d-203">Se a primeira tentativa de reconexão falhar, a segunda tentativa de reconexão também será iniciada imediatamente, em vez de esperar 2 segundos, como seria na configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-203">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="0b77d-204">Se a segunda tentativa de reconexão falhar, a terceira tentativa de reconexão será iniciada em 10 segundos, o que é novamente como a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-204">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="0b77d-205">O comportamento personalizado, em seguida, deriva novamente do comportamento padrão ao parar após a terceira tentativa de reconexão, em vez de tentar mais uma tentativa de reconexão em outros 30 segundos, como seria na configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-205">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="0b77d-206">Se você quiser ainda mais controle sobre o tempo e o número de tentativas de reconexão automática, o `withAutomaticReconnect` aceitará um objeto que implementa a `IRetryPolicy` interface, que tem um único método chamado `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-206">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="0b77d-207">`nextRetryDelayInMilliseconds` usa um único argumento com o tipo `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-207">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="0b77d-208">O `RetryContext` tem três propriedades: `previousRetryCount` , `elapsedMilliseconds` e `retryReason` que são a `number` , a `number` e uma `Error` respectivamente.</span><span class="sxs-lookup"><span data-stu-id="0b77d-208">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="0b77d-209">Antes da primeira tentativa de reconexão, `previousRetryCount` e `elapsedMilliseconds` será zero, e o `retryReason` será o erro que causou a perda da conexão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-209">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="0b77d-210">Após cada tentativa de repetição com falha, o `previousRetryCount` será incrementado em um, `elapsedMilliseconds` será atualizado para refletir a quantidade de tempo gasto reconectando até agora em milissegundos, e o `retryReason` será o erro que causou a falha da última tentativa de reconexão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-210">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="0b77d-211">`nextRetryDelayInMilliseconds` deve retornar um número que representa o número de milissegundos a aguardar antes da próxima tentativa de reconexão ou `null` se o `HubConnection` deve parar de reconectar.</span><span class="sxs-lookup"><span data-stu-id="0b77d-211">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="0b77d-212">Como alternativa, você pode escrever um código que reconectará o cliente manualmente, conforme demonstrado na [reconexão manual](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="0b77d-212">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="0b77d-213">Reconectar manualmente</span><span class="sxs-lookup"><span data-stu-id="0b77d-213">Manually reconnect</span></span>

<span data-ttu-id="0b77d-214">O código a seguir demonstra uma abordagem de reconexão manual típica:</span><span class="sxs-lookup"><span data-stu-id="0b77d-214">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="0b77d-215">Uma função (nesse caso, a `start` função) é criada para iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-215">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="0b77d-216">Chame a `start` função no manipulador de eventos da conexão `onclose` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-216">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/:::no-loc(SignalR):::Chat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="0b77d-217">Uma implementação do mundo real usaria um retirada exponencial ou tentaria um número especificado de vezes antes de desistir.</span><span class="sxs-lookup"><span data-stu-id="0b77d-217">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0b77d-218">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0b77d-218">Additional resources</span></span>

* [<span data-ttu-id="0b77d-219">Referência da API JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b77d-219">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="0b77d-220">Tutorial do JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b77d-220">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0b77d-221">Tutorial do webpack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="0b77d-221">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="0b77d-222">Hubs</span><span class="sxs-lookup"><span data-stu-id="0b77d-222">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="0b77d-223">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="0b77d-223">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0b77d-224">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="0b77d-224">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="0b77d-225">Solicitações entre origens (CORS)</span><span class="sxs-lookup"><span data-stu-id="0b77d-225">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="0b77d-226">Documentação sem servidor do serviço do Azure :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="0b77d-226">Azure :::no-loc(SignalR)::: Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
* [<span data-ttu-id="0b77d-227">Solucionar erros de conexão</span><span class="sxs-lookup"><span data-stu-id="0b77d-227">Troubleshoot connection errors</span></span>](xref:signalr/troubleshoot)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0b77d-228">Por [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="0b77d-228">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="0b77d-229">A :::no-loc(SignalR)::: biblioteca de cliente ASP.NET Core JavaScript permite que os desenvolvedores chamem o código de Hub do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="0b77d-229">The ASP.NET Core :::no-loc(SignalR)::: JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="0b77d-230">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0b77d-230">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="0b77d-231">Instalar o :::no-loc(SignalR)::: pacote do cliente</span><span class="sxs-lookup"><span data-stu-id="0b77d-231">Install the :::no-loc(SignalR)::: client package</span></span>

<span data-ttu-id="0b77d-232">A :::no-loc(SignalR)::: biblioteca de cliente JavaScript é entregue como um pacote [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="0b77d-232">The :::no-loc(SignalR)::: JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="0b77d-233">As seções a seguir descrevem diferentes maneiras de instalar a biblioteca de cliente.</span><span class="sxs-lookup"><span data-stu-id="0b77d-233">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="0b77d-234">Instalar com NPM</span><span class="sxs-lookup"><span data-stu-id="0b77d-234">Install with npm</span></span>

<span data-ttu-id="0b77d-235">Se estiver usando o Visual Studio, execute os seguintes comandos no **console do Gerenciador de pacotes** enquanto estiver na pasta raiz.</span><span class="sxs-lookup"><span data-stu-id="0b77d-235">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="0b77d-236">Para Visual Studio Code, execute os seguintes comandos do **terminal integrado** .</span><span class="sxs-lookup"><span data-stu-id="0b77d-236">For Visual Studio Code, run the following commands from the **Integrated Terminal** .</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="0b77d-237">NPM instala o conteúdo do pacote na *pasta \\ @aspnet\signalr\dist\browser node_modules* .</span><span class="sxs-lookup"><span data-stu-id="0b77d-237">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="0b77d-238">Crie uma nova pasta chamada *signalr* na pasta *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="0b77d-238">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="0b77d-239">Copie o arquivo de *signalr.js* para a pasta *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="0b77d-239">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="0b77d-240">Referencie o :::no-loc(SignalR)::: cliente JavaScript no `<script>` elemento.</span><span class="sxs-lookup"><span data-stu-id="0b77d-240">Reference the :::no-loc(SignalR)::: JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="0b77d-241">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0b77d-241">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="0b77d-242">Usar uma CDN (rede de distribuição de conteúdo)</span><span class="sxs-lookup"><span data-stu-id="0b77d-242">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="0b77d-243">Para usar a biblioteca de cliente sem o pré-requisito NPM, faça referência a uma cópia hospedada em CDN da biblioteca de cliente.</span><span class="sxs-lookup"><span data-stu-id="0b77d-243">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="0b77d-244">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0b77d-244">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="0b77d-245">A biblioteca de cliente está disponível no seguinte CDNs:</span><span class="sxs-lookup"><span data-stu-id="0b77d-245">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="0b77d-246">cdnjs</span><span class="sxs-lookup"><span data-stu-id="0b77d-246">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="0b77d-247">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="0b77d-247">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="0b77d-248">unpkg</span><span class="sxs-lookup"><span data-stu-id="0b77d-248">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="0b77d-249">Instalar com LibMan</span><span class="sxs-lookup"><span data-stu-id="0b77d-249">Install with LibMan</span></span>

<span data-ttu-id="0b77d-250">[LibMan](xref:client-side/libman/index) pode ser usado para instalar arquivos de biblioteca de cliente específicos da biblioteca de cliente hospedada na CDN.</span><span class="sxs-lookup"><span data-stu-id="0b77d-250">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="0b77d-251">Por exemplo, adicione apenas o arquivo JavaScript reduzidos ao projeto.</span><span class="sxs-lookup"><span data-stu-id="0b77d-251">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="0b77d-252">Para obter detalhes sobre essa abordagem, consulte [Adicionar a :::no-loc(SignalR)::: biblioteca de cliente](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="0b77d-252">For details on that approach, see [Add the :::no-loc(SignalR)::: client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="0b77d-253">Conectar-se a um hub</span><span class="sxs-lookup"><span data-stu-id="0b77d-253">Connect to a hub</span></span>

<span data-ttu-id="0b77d-254">O código a seguir cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-254">The following code creates and starts a connection.</span></span> <span data-ttu-id="0b77d-255">O nome do Hub não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="0b77d-255">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="0b77d-256">Conexões entre origens</span><span class="sxs-lookup"><span data-stu-id="0b77d-256">Cross-origin connections</span></span>

<span data-ttu-id="0b77d-257">Normalmente, os navegadores carregam conexões do mesmo domínio que a página solicitada.</span><span class="sxs-lookup"><span data-stu-id="0b77d-257">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="0b77d-258">No entanto, há ocasiões em que uma conexão com outro domínio é necessária.</span><span class="sxs-lookup"><span data-stu-id="0b77d-258">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="0b77d-259">Para impedir que um site mal-intencionado leia dados confidenciais de outro site, [as conexões entre origens](xref:security/cors) são desabilitadas por padrão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-259">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="0b77d-260">Para permitir uma solicitação entre origens, habilite-a na `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="0b77d-260">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="0b77d-261">Métodos do hub de chamadas do cliente</span><span class="sxs-lookup"><span data-stu-id="0b77d-261">Call hub methods from client</span></span>

<span data-ttu-id="0b77d-262">Os clientes JavaScript chamam métodos públicos em hubs por meio do método [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="0b77d-262">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="0b77d-263">O `invoke` método aceita dois argumentos:</span><span class="sxs-lookup"><span data-stu-id="0b77d-263">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="0b77d-264">O nome do método de Hub.</span><span class="sxs-lookup"><span data-stu-id="0b77d-264">The name of the hub method.</span></span> <span data-ttu-id="0b77d-265">No exemplo a seguir, o nome do método no Hub é `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-265">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="0b77d-266">Quaisquer argumentos definidos no método Hub.</span><span class="sxs-lookup"><span data-stu-id="0b77d-266">Any arguments defined in the hub method.</span></span> <span data-ttu-id="0b77d-267">No exemplo a seguir, o nome do argumento é `message` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-267">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="0b77d-268">O código de exemplo usa a sintaxe de função de seta que tem suporte nas versões atuais de todos os principais navegadores, exceto o Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="0b77d-268">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="0b77d-269">Somente há suporte para métodos de Hub de chamada de um cliente ao usar o serviço do Azure :::no-loc(SignalR)::: no modo *padrão* .</span><span class="sxs-lookup"><span data-stu-id="0b77d-269">Calling hub methods from a client is only supported when using the Azure :::no-loc(SignalR)::: Service in *Default* mode.</span></span> <span data-ttu-id="0b77d-270">Para obter mais informações, consulte perguntas frequentes [(repositório GitHub do Azure-signalr)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="0b77d-270">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="0b77d-271">O `invoke` método retorna uma [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0b77d-271">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="0b77d-272">O `Promise` é resolvido com o valor de retorno (se houver) quando o método no servidor retorna.</span><span class="sxs-lookup"><span data-stu-id="0b77d-272">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="0b77d-273">Se o método no servidor gerar um erro, o `Promise` será rejeitado com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="0b77d-273">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="0b77d-274">Use os `then` `catch` métodos e na `Promise` própria para lidar com esses casos (ou `await` sintaxe).</span><span class="sxs-lookup"><span data-stu-id="0b77d-274">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="0b77d-275">O `send` método retorna um JavaScript `Promise` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-275">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="0b77d-276">O `Promise` é resolvido quando a mensagem é enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="0b77d-276">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="0b77d-277">Se houver um erro ao enviar a mensagem, o `Promise` será rejeitado com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="0b77d-277">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="0b77d-278">Use os `then` `catch` métodos e na `Promise` própria para lidar com esses casos (ou `await` sintaxe).</span><span class="sxs-lookup"><span data-stu-id="0b77d-278">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="0b77d-279">`send`O uso de não aguarda até que o servidor tenha recebido a mensagem.</span><span class="sxs-lookup"><span data-stu-id="0b77d-279">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="0b77d-280">Consequentemente, não é possível retornar dados ou erros do servidor.</span><span class="sxs-lookup"><span data-stu-id="0b77d-280">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="0b77d-281">Chamar métodos de cliente do Hub</span><span class="sxs-lookup"><span data-stu-id="0b77d-281">Call client methods from hub</span></span>

<span data-ttu-id="0b77d-282">Para receber mensagens do Hub, defina um método usando o método [on](/javascript/api/%40aspnet/signalr/hubconnection#on) do `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-282">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="0b77d-283">O nome do método de cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0b77d-283">The name of the JavaScript client method.</span></span> <span data-ttu-id="0b77d-284">No exemplo a seguir, o nome do método é `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-284">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="0b77d-285">Argumentos que o Hub passa para o método.</span><span class="sxs-lookup"><span data-stu-id="0b77d-285">Arguments the hub passes to the method.</span></span> <span data-ttu-id="0b77d-286">No exemplo a seguir, o valor do argumento é `message` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-286">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="0b77d-287">O código anterior em `connection.on` é executado quando o código do lado do servidor o chama usando o <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> método.</span><span class="sxs-lookup"><span data-stu-id="0b77d-287">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="0b77d-288">:::no-loc(SignalR)::: determina qual método de cliente chamar correspondendo o nome do método e os argumentos definidos em `SendAsync` e `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-288">:::no-loc(SignalR)::: determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="0b77d-289">Como prática recomendada, chame o método [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) no `HubConnection` After `on` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-289">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="0b77d-290">Isso garante que seus manipuladores sejam registrados antes que todas as mensagens sejam recebidas.</span><span class="sxs-lookup"><span data-stu-id="0b77d-290">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="0b77d-291">Registro em log e tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="0b77d-291">Error handling and logging</span></span>

<span data-ttu-id="0b77d-292">Encadear um `catch` método ao final do `start` método para manipular erros do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="0b77d-292">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="0b77d-293">Use `console.error` para gerar erros de saída para o console do navegador.</span><span class="sxs-lookup"><span data-stu-id="0b77d-293">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="0b77d-294">Configure o rastreamento de log no lado do cliente passando um agente e um tipo de evento para registrar quando a conexão é feita.</span><span class="sxs-lookup"><span data-stu-id="0b77d-294">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="0b77d-295">As mensagens são registradas com o nível de log especificado e superior.</span><span class="sxs-lookup"><span data-stu-id="0b77d-295">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="0b77d-296">Os níveis de log disponíveis são os seguintes:</span><span class="sxs-lookup"><span data-stu-id="0b77d-296">Available log levels are as follows:</span></span>

* <span data-ttu-id="0b77d-297">`signalR.LogLevel.Error`: Mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="0b77d-297">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="0b77d-298">Registra `Error` somente mensagens.</span><span class="sxs-lookup"><span data-stu-id="0b77d-298">Logs `Error` messages only.</span></span>
* <span data-ttu-id="0b77d-299">`signalR.LogLevel.Warning`: Mensagens de aviso sobre erros potenciais.</span><span class="sxs-lookup"><span data-stu-id="0b77d-299">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="0b77d-300">Logs `Warning` e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="0b77d-300">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="0b77d-301">`signalR.LogLevel.Information`: Mensagens de status sem erros.</span><span class="sxs-lookup"><span data-stu-id="0b77d-301">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="0b77d-302">Logs `Information` , `Warning` e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="0b77d-302">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="0b77d-303">`signalR.LogLevel.Trace`: Rastrear mensagens.</span><span class="sxs-lookup"><span data-stu-id="0b77d-303">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="0b77d-304">Registra tudo, incluindo dados transportados entre o Hub e o cliente.</span><span class="sxs-lookup"><span data-stu-id="0b77d-304">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="0b77d-305">Use o método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) no [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar o nível de log.</span><span class="sxs-lookup"><span data-stu-id="0b77d-305">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="0b77d-306">As mensagens são registradas no console do navegador.</span><span class="sxs-lookup"><span data-stu-id="0b77d-306">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="0b77d-307">Reconectar clientes</span><span class="sxs-lookup"><span data-stu-id="0b77d-307">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="0b77d-308">Reconectar manualmente</span><span class="sxs-lookup"><span data-stu-id="0b77d-308">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="0b77d-309">Antes de 3,0, o cliente JavaScript para :::no-loc(SignalR)::: não se reconectar automaticamente.</span><span class="sxs-lookup"><span data-stu-id="0b77d-309">Prior to 3.0, the JavaScript client for :::no-loc(SignalR)::: doesn't automatically reconnect.</span></span> <span data-ttu-id="0b77d-310">Você deve escrever um código que reconectará o cliente manualmente.</span><span class="sxs-lookup"><span data-stu-id="0b77d-310">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="0b77d-311">O código a seguir demonstra uma abordagem de reconexão manual típica:</span><span class="sxs-lookup"><span data-stu-id="0b77d-311">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="0b77d-312">Uma função (nesse caso, a `start` função) é criada para iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="0b77d-312">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="0b77d-313">Chame a `start` função no manipulador de eventos da conexão `onclose` .</span><span class="sxs-lookup"><span data-stu-id="0b77d-313">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/:::no-loc(SignalR):::Chat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="0b77d-314">Uma implementação do mundo real usaria um retirada exponencial ou tentaria um número especificado de vezes antes de desistir.</span><span class="sxs-lookup"><span data-stu-id="0b77d-314">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0b77d-315">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0b77d-315">Additional resources</span></span>

* [<span data-ttu-id="0b77d-316">Referência da API JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b77d-316">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="0b77d-317">Tutorial do JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b77d-317">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="0b77d-318">Tutorial do webpack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="0b77d-318">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="0b77d-319">Hubs</span><span class="sxs-lookup"><span data-stu-id="0b77d-319">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="0b77d-320">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="0b77d-320">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0b77d-321">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="0b77d-321">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="0b77d-322">Solicitações entre origens (CORS)</span><span class="sxs-lookup"><span data-stu-id="0b77d-322">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="0b77d-323">Documentação sem servidor do serviço do Azure :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="0b77d-323">Azure :::no-loc(SignalR)::: Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
