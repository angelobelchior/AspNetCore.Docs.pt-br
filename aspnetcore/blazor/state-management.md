---
title: BlazorGerenciamento de estado ASP.NET Core
author: guardrex
description: Saiba como persistir o estado em Blazor Server aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 28ca3b5c4472dc21e709d01705dc64168107ca61
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013548"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="b71d5-103">BlazorGerenciamento de estado ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b71d5-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="b71d5-104">Por [Steve Sanderson](https://github.com/SteveSandersonMS) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b71d5-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="b71d5-105">O estado do usuário criado em um Blazor WebAssembly aplicativo é mantido na memória do navegador.</span><span class="sxs-lookup"><span data-stu-id="b71d5-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="b71d5-106">Exemplos de estado do usuário mantido na memória do navegador incluem:</span><span class="sxs-lookup"><span data-stu-id="b71d5-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="b71d5-107">A hierarquia de instâncias de componente e sua saída de renderização mais recente na interface do usuário renderizada.</span><span class="sxs-lookup"><span data-stu-id="b71d5-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="b71d5-108">Os valores de campos e propriedades em instâncias de componente.</span><span class="sxs-lookup"><span data-stu-id="b71d5-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="b71d5-109">Dados mantidos em instâncias de serviço de [injeção de dependência (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="b71d5-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="b71d5-110">Os valores definidos por meio de chamadas de [interoperabilidade do JavaScript](xref:blazor/call-javascript-from-dotnet) .</span><span class="sxs-lookup"><span data-stu-id="b71d5-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="b71d5-111">Quando um usuário fecha e abre novamente o navegador ou recarrega a página, o estado do usuário mantido na memória do navegador é perdido.</span><span class="sxs-lookup"><span data-stu-id="b71d5-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="b71d5-112">Persistir o estado entre as sessões do navegador</span><span class="sxs-lookup"><span data-stu-id="b71d5-112">Persist state across browser sessions</span></span>

<span data-ttu-id="b71d5-113">Em geral, mantenha o estado entre as sessões de navegador onde os usuários estão criando dados ativamente, não simplesmente lendo dados já existentes.</span><span class="sxs-lookup"><span data-stu-id="b71d5-113">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="b71d5-114">Para preservar o estado entre as sessões do navegador, o aplicativo deve manter os dados em algum outro local de armazenamento que a memória do navegador.</span><span class="sxs-lookup"><span data-stu-id="b71d5-114">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="b71d5-115">A persistência de estado não é automática.</span><span class="sxs-lookup"><span data-stu-id="b71d5-115">State persistence isn't automatic.</span></span> <span data-ttu-id="b71d5-116">Você deve executar etapas ao desenvolver o aplicativo para implementar a persistência de dados com estado.</span><span class="sxs-lookup"><span data-stu-id="b71d5-116">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="b71d5-117">A persistência de dados normalmente é necessária apenas para o estado de alto valor que os usuários gastaram esforços para criar.</span><span class="sxs-lookup"><span data-stu-id="b71d5-117">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="b71d5-118">Nos exemplos a seguir, o estado de persistência economiza tempo ou ajuda em atividades comerciais:</span><span class="sxs-lookup"><span data-stu-id="b71d5-118">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="b71d5-119">Web Forms de várias etapas: é demorado para um usuário reinserir dados para várias etapas concluídas de um formulário da Web de várias etapas se seu estado for perdido.</span><span class="sxs-lookup"><span data-stu-id="b71d5-119">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="b71d5-120">Um usuário perde o estado nesse cenário se ele navega para fora do formulário e retorna mais tarde.</span><span class="sxs-lookup"><span data-stu-id="b71d5-120">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="b71d5-121">Carrinhos de compras: qualquer componente comercialmente importante de um aplicativo que representa a possível receita pode ser mantido.</span><span class="sxs-lookup"><span data-stu-id="b71d5-121">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="b71d5-122">Um usuário que perde seu estado e, portanto, seu carrinho de compras, pode comprar menos produtos ou serviços quando eles retornam para o site mais tarde.</span><span class="sxs-lookup"><span data-stu-id="b71d5-122">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="b71d5-123">Um aplicativo só pode persistir o *estado do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="b71d5-123">An app can only persist *app state*.</span></span> <span data-ttu-id="b71d5-124">As UIs não podem ser persistentes, como instâncias de componente e suas árvores de renderização.</span><span class="sxs-lookup"><span data-stu-id="b71d5-124">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="b71d5-125">Os componentes e as árvores de renderização geralmente não são serializáveis.</span><span class="sxs-lookup"><span data-stu-id="b71d5-125">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="b71d5-126">Para persistir o estado da interface do usuário, como os nós expandidos de um controle de exibição de árvore, o aplicativo deve usar o código personalizado para modelar o comportamento do estado da interface do usuário como estado do aplicativo serializável.</span><span class="sxs-lookup"><span data-stu-id="b71d5-126">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="b71d5-127">Onde persistir o estado</span><span class="sxs-lookup"><span data-stu-id="b71d5-127">Where to persist state</span></span>

<span data-ttu-id="b71d5-128">Existem três locais comuns para o estado persistente:</span><span class="sxs-lookup"><span data-stu-id="b71d5-128">Three common locations exist for persisting state:</span></span>

* [<span data-ttu-id="b71d5-129">Armazenamento no lado do servidor</span><span class="sxs-lookup"><span data-stu-id="b71d5-129">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="b71d5-130">URL</span><span class="sxs-lookup"><span data-stu-id="b71d5-130">URL</span></span>](#url)
* [<span data-ttu-id="b71d5-131">Armazenamento de navegador</span><span class="sxs-lookup"><span data-stu-id="b71d5-131">Browser storage</span></span>](#browser-storage)

### <a name="server-side-storage"></a><span data-ttu-id="b71d5-132">Armazenamento no lado do servidor</span><span class="sxs-lookup"><span data-stu-id="b71d5-132">Server-side storage</span></span>

<span data-ttu-id="b71d5-133">Para persistência de dados permanente que abrange vários usuários e dispositivos, o aplicativo pode usar armazenamento independente do lado do servidor acessado por meio de uma API da Web.</span><span class="sxs-lookup"><span data-stu-id="b71d5-133">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="b71d5-134">As opções incluem:</span><span class="sxs-lookup"><span data-stu-id="b71d5-134">Options include:</span></span>

* <span data-ttu-id="b71d5-135">Armazenamento de Blobs</span><span class="sxs-lookup"><span data-stu-id="b71d5-135">Blob storage</span></span>
* <span data-ttu-id="b71d5-136">Armazenamento de chave-valor</span><span class="sxs-lookup"><span data-stu-id="b71d5-136">Key-value storage</span></span>
* <span data-ttu-id="b71d5-137">Banco de dados relacional</span><span class="sxs-lookup"><span data-stu-id="b71d5-137">Relational database</span></span>
* <span data-ttu-id="b71d5-138">Armazenamento de tabelas</span><span class="sxs-lookup"><span data-stu-id="b71d5-138">Table storage</span></span>

<span data-ttu-id="b71d5-139">Depois que os dados são salvos, o estado do usuário é mantido e está disponível em qualquer nova sessão do navegador.</span><span class="sxs-lookup"><span data-stu-id="b71d5-139">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="b71d5-140">Como Blazor WebAssembly os aplicativos são executados inteiramente no navegador do usuário, eles exigem medidas adicionais para acessar sistemas externos seguros, como bancos de dados e serviços de armazenamento.</span><span class="sxs-lookup"><span data-stu-id="b71d5-140">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="b71d5-141">Blazor WebAssemblyos aplicativos são protegidos da mesma maneira que os aplicativos de página única (SPAs).</span><span class="sxs-lookup"><span data-stu-id="b71d5-141">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="b71d5-142">Normalmente, um aplicativo autentica um usuário por meio [OAuth](https://oauth.net) / [do OAuth OpenID Connect (OIDC)](https://openid.net/connect/) e, em seguida, interage com os serviços de armazenamento e bancos de dados por meio de chamadas à API da Web para um aplicativo do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="b71d5-142">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="b71d5-143">O aplicativo do lado do servidor Media a transferência de dados entre o Blazor WebAssembly aplicativo e o serviço ou banco de dados do armazenamento.</span><span class="sxs-lookup"><span data-stu-id="b71d5-143">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="b71d5-144">O Blazor WebAssembly aplicativo mantém uma conexão efêmera com o aplicativo do lado do servidor, enquanto o aplicativo do lado do servidor tem uma conexão persistente com o armazenamento.</span><span class="sxs-lookup"><span data-stu-id="b71d5-144">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="b71d5-145">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="b71d5-145">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="b71d5-146">Blazor\*Segurança e Identity \* los</span><span class="sxs-lookup"><span data-stu-id="b71d5-146">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="b71d5-147">Para obter mais informações sobre as opções de armazenamento de dados do Azure, consulte o seguinte:</span><span class="sxs-lookup"><span data-stu-id="b71d5-147">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="b71d5-148">Bancos de dados do Azure</span><span class="sxs-lookup"><span data-stu-id="b71d5-148">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="b71d5-149">Documentação do armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="b71d5-149">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="b71d5-150">URL</span><span class="sxs-lookup"><span data-stu-id="b71d5-150">URL</span></span>

<span data-ttu-id="b71d5-151">Para dados transitórios que representam o estado de navegação, modele os dados como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="b71d5-151">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="b71d5-152">Exemplos de estado de usuário modelados na URL incluem:</span><span class="sxs-lookup"><span data-stu-id="b71d5-152">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="b71d5-153">A ID de uma entidade exibida.</span><span class="sxs-lookup"><span data-stu-id="b71d5-153">The ID of a viewed entity.</span></span>
* <span data-ttu-id="b71d5-154">O número da página atual em uma grade paginável.</span><span class="sxs-lookup"><span data-stu-id="b71d5-154">The current page number in a paged grid.</span></span>

<span data-ttu-id="b71d5-155">O conteúdo da barra de endereços do navegador será retido se o usuário recarregar a página manualmente.</span><span class="sxs-lookup"><span data-stu-id="b71d5-155">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="b71d5-156">Para obter informações sobre como definir padrões de URL com a [`@page`](xref:mvc/views/razor#page) diretiva, consulte <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="b71d5-156">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="b71d5-157">Armazenamento de navegador</span><span class="sxs-lookup"><span data-stu-id="b71d5-157">Browser storage</span></span>

<span data-ttu-id="b71d5-158">Para dados transitórios que o usuário está criando ativamente, um local de armazenamento usado normalmente é o navegador [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) e as [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) coleções:</span><span class="sxs-lookup"><span data-stu-id="b71d5-158">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="b71d5-159">`localStorage`está no escopo da janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="b71d5-159">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="b71d5-160">Se o usuário recarregar a página ou fechar e abrir novamente o navegador, o estado persistirá.</span><span class="sxs-lookup"><span data-stu-id="b71d5-160">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="b71d5-161">Se o usuário abrir várias guias do navegador, o estado será compartilhado entre as guias.</span><span class="sxs-lookup"><span data-stu-id="b71d5-161">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="b71d5-162">Os dados persistem `localStorage` até serem explicitamente desmarcados.</span><span class="sxs-lookup"><span data-stu-id="b71d5-162">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="b71d5-163">`sessionStorage`está no escopo da guia navegador. Se o usuário recarregar a guia, o estado persiste.</span><span class="sxs-lookup"><span data-stu-id="b71d5-163">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="b71d5-164">Se o usuário fechar a guia ou o navegador, o estado será perdido.</span><span class="sxs-lookup"><span data-stu-id="b71d5-164">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="b71d5-165">Se o usuário abrir várias guias do navegador, cada guia terá sua própria versão independente dos dados.</span><span class="sxs-lookup"><span data-stu-id="b71d5-165">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="b71d5-166">`localStorage`e `sessionStorage` pode ser usado em Blazor WebAssembly aplicativos, mas apenas escrevendo código personalizado ou usando um pacote de terceiros.</span><span class="sxs-lookup"><span data-stu-id="b71d5-166">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="b71d5-167">Em geral, `sessionStorage` é mais seguro usar.</span><span class="sxs-lookup"><span data-stu-id="b71d5-167">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="b71d5-168">`sessionStorage`evita o risco de um usuário abrir várias guias e encontrar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="b71d5-168">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="b71d5-169">Bugs no armazenamento de estado entre guias.</span><span class="sxs-lookup"><span data-stu-id="b71d5-169">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="b71d5-170">Comportamento confuso quando uma guia substitui o estado de outras guias.</span><span class="sxs-lookup"><span data-stu-id="b71d5-170">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="b71d5-171">`localStorage`é a melhor opção se o aplicativo precisar manter o estado entre fechar e abrir novamente o navegador.</span><span class="sxs-lookup"><span data-stu-id="b71d5-171">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="b71d5-172">Os usuários podem exibir ou adulterar os dados armazenados no `localStorage` e no `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="b71d5-172">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b71d5-173">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b71d5-173">Additional resources</span></span>

* [<span data-ttu-id="b71d5-174">Salvar o estado do aplicativo antes de uma operação de autenticação</span><span class="sxs-lookup"><span data-stu-id="b71d5-174">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="b71d5-175">Blazor Serveré uma estrutura de aplicativo com estado.</span><span class="sxs-lookup"><span data-stu-id="b71d5-175">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="b71d5-176">Na maioria das vezes, o aplicativo mantém uma conexão com o servidor.</span><span class="sxs-lookup"><span data-stu-id="b71d5-176">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="b71d5-177">O estado do usuário é mantido na memória do servidor em um *circuito*.</span><span class="sxs-lookup"><span data-stu-id="b71d5-177">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="b71d5-178">Exemplos de estado do usuário mantido em um circuito incluem:</span><span class="sxs-lookup"><span data-stu-id="b71d5-178">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="b71d5-179">A hierarquia de instâncias de componente e sua saída de renderização mais recente na interface do usuário renderizada.</span><span class="sxs-lookup"><span data-stu-id="b71d5-179">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="b71d5-180">Os valores de campos e propriedades em instâncias de componente.</span><span class="sxs-lookup"><span data-stu-id="b71d5-180">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="b71d5-181">Dados mantidos em instâncias de serviço de [injeção de dependência (di)](xref:fundamentals/dependency-injection) que estão no escopo do circuito.</span><span class="sxs-lookup"><span data-stu-id="b71d5-181">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="b71d5-182">O estado do usuário também pode ser encontrado em variáveis JavaScript no conjunto de memória do navegador por meio de chamadas de [interoperabilidade do JavaScript](xref:blazor/call-javascript-from-dotnet) .</span><span class="sxs-lookup"><span data-stu-id="b71d5-182">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="b71d5-183">Se um usuário sofrer uma perda de conexão de rede temporária, Blazor o tentará reconectar o usuário ao seu circuito original com seu estado original.</span><span class="sxs-lookup"><span data-stu-id="b71d5-183">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="b71d5-184">No entanto, a reconexão de um usuário ao seu circuito original na memória do servidor nem sempre é possível:</span><span class="sxs-lookup"><span data-stu-id="b71d5-184">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="b71d5-185">O servidor não pode manter um circuito desconectado para sempre.</span><span class="sxs-lookup"><span data-stu-id="b71d5-185">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="b71d5-186">O servidor deve liberar um circuito desconectado após um tempo limite ou quando o servidor está sob pressão de memória.</span><span class="sxs-lookup"><span data-stu-id="b71d5-186">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="b71d5-187">Em ambientes de implantação com balanceamento de carga e vários servidores, os servidores individuais podem falhar ou ser removidos automaticamente quando não forem mais necessários para lidar com o volume geral de solicitações.</span><span class="sxs-lookup"><span data-stu-id="b71d5-187">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="b71d5-188">O servidor original solicitações de processamento de um usuário pode ficar indisponível quando o usuário tenta se reconectar.</span><span class="sxs-lookup"><span data-stu-id="b71d5-188">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="b71d5-189">O usuário pode fechar e reabrir o navegador ou recarregar a página, o que remove qualquer estado mantido na memória do navegador.</span><span class="sxs-lookup"><span data-stu-id="b71d5-189">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="b71d5-190">Por exemplo, os valores de variáveis JavaScript definidos por meio de chamadas de interoperabilidade JavaScript são perdidos.</span><span class="sxs-lookup"><span data-stu-id="b71d5-190">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="b71d5-191">Quando um usuário não pode ser reconectado ao seu circuito original, o usuário recebe um novo circuito com um estado vazio.</span><span class="sxs-lookup"><span data-stu-id="b71d5-191">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="b71d5-192">Isso é equivalente a fechar e reabrir um aplicativo de área de trabalho.</span><span class="sxs-lookup"><span data-stu-id="b71d5-192">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="b71d5-193">Persistir estado entre circuitos</span><span class="sxs-lookup"><span data-stu-id="b71d5-193">Persist state across circuits</span></span>

<span data-ttu-id="b71d5-194">Em geral, mantenha o estado entre circuitos em que os usuários estão criando dados ativamente, não simplesmente lendo dados já existentes.</span><span class="sxs-lookup"><span data-stu-id="b71d5-194">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="b71d5-195">Para preservar o estado entre circuitos, o aplicativo deve manter os dados em algum outro local de armazenamento que a memória do servidor.</span><span class="sxs-lookup"><span data-stu-id="b71d5-195">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="b71d5-196">A persistência de estado não é automática.</span><span class="sxs-lookup"><span data-stu-id="b71d5-196">State persistence isn't automatic.</span></span> <span data-ttu-id="b71d5-197">Você deve executar etapas ao desenvolver o aplicativo para implementar a persistência de dados com estado.</span><span class="sxs-lookup"><span data-stu-id="b71d5-197">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="b71d5-198">A persistência de dados normalmente é necessária apenas para o estado de alto valor que os usuários gastaram esforços para criar.</span><span class="sxs-lookup"><span data-stu-id="b71d5-198">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="b71d5-199">Nos exemplos a seguir, o estado de persistência economiza tempo ou ajuda em atividades comerciais:</span><span class="sxs-lookup"><span data-stu-id="b71d5-199">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="b71d5-200">Web Forms de várias etapas: é demorado para um usuário reinserir dados para várias etapas concluídas de um formulário da Web de várias etapas se seu estado for perdido.</span><span class="sxs-lookup"><span data-stu-id="b71d5-200">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="b71d5-201">Um usuário perde o estado nesse cenário se ele navega para fora do formulário e retorna mais tarde.</span><span class="sxs-lookup"><span data-stu-id="b71d5-201">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="b71d5-202">Carrinhos de compras: qualquer componente comercialmente importante de um aplicativo que representa a possível receita pode ser mantido.</span><span class="sxs-lookup"><span data-stu-id="b71d5-202">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="b71d5-203">Um usuário que perde seu estado e, portanto, seu carrinho de compras, pode comprar menos produtos ou serviços quando eles retornam para o site mais tarde.</span><span class="sxs-lookup"><span data-stu-id="b71d5-203">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="b71d5-204">Um aplicativo só pode persistir o *estado do aplicativo*.</span><span class="sxs-lookup"><span data-stu-id="b71d5-204">An app can only persist *app state*.</span></span> <span data-ttu-id="b71d5-205">As UIs não podem ser persistentes, como instâncias de componente e suas árvores de renderização.</span><span class="sxs-lookup"><span data-stu-id="b71d5-205">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="b71d5-206">Os componentes e as árvores de renderização geralmente não são serializáveis.</span><span class="sxs-lookup"><span data-stu-id="b71d5-206">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="b71d5-207">Para persistir o estado da interface do usuário, como os nós expandidos de um controle de exibição de árvore, o aplicativo deve usar o código personalizado para modelar o comportamento do estado da interface do usuário como estado do aplicativo serializável.</span><span class="sxs-lookup"><span data-stu-id="b71d5-207">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="b71d5-208">Onde persistir o estado</span><span class="sxs-lookup"><span data-stu-id="b71d5-208">Where to persist state</span></span>

<span data-ttu-id="b71d5-209">Existem três locais comuns para o estado persistente:</span><span class="sxs-lookup"><span data-stu-id="b71d5-209">Three common locations exist for persisting state:</span></span>

* [<span data-ttu-id="b71d5-210">Armazenamento no lado do servidor</span><span class="sxs-lookup"><span data-stu-id="b71d5-210">Server-side storage</span></span>](#server-side-storage)
* [<span data-ttu-id="b71d5-211">URL</span><span class="sxs-lookup"><span data-stu-id="b71d5-211">URL</span></span>](#url)
* [<span data-ttu-id="b71d5-212">Armazenamento de navegador</span><span class="sxs-lookup"><span data-stu-id="b71d5-212">Browser storage</span></span>](#browser-storage)

### <a name="server-side-storage"></a><span data-ttu-id="b71d5-213">Armazenamento no lado do servidor</span><span class="sxs-lookup"><span data-stu-id="b71d5-213">Server-side storage</span></span>

<span data-ttu-id="b71d5-214">Para persistência de dados permanente que abrange vários usuários e dispositivos, o aplicativo pode usar o armazenamento do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="b71d5-214">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="b71d5-215">As opções incluem:</span><span class="sxs-lookup"><span data-stu-id="b71d5-215">Options include:</span></span>

* <span data-ttu-id="b71d5-216">Armazenamento de Blobs</span><span class="sxs-lookup"><span data-stu-id="b71d5-216">Blob storage</span></span>
* <span data-ttu-id="b71d5-217">Armazenamento de chave-valor</span><span class="sxs-lookup"><span data-stu-id="b71d5-217">Key-value storage</span></span>
* <span data-ttu-id="b71d5-218">Banco de dados relacional</span><span class="sxs-lookup"><span data-stu-id="b71d5-218">Relational database</span></span>
* <span data-ttu-id="b71d5-219">Armazenamento de tabelas</span><span class="sxs-lookup"><span data-stu-id="b71d5-219">Table storage</span></span>

<span data-ttu-id="b71d5-220">Depois que os dados são salvos, o estado do usuário é mantido e está disponível em qualquer circuito novo.</span><span class="sxs-lookup"><span data-stu-id="b71d5-220">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="b71d5-221">Para obter mais informações sobre as opções de armazenamento de dados do Azure, consulte o seguinte:</span><span class="sxs-lookup"><span data-stu-id="b71d5-221">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="b71d5-222">Bancos de dados do Azure</span><span class="sxs-lookup"><span data-stu-id="b71d5-222">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="b71d5-223">Documentação do armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="b71d5-223">Azure Storage Documentation</span></span>](/azure/storage/)

### <a name="url"></a><span data-ttu-id="b71d5-224">URL</span><span class="sxs-lookup"><span data-stu-id="b71d5-224">URL</span></span>

<span data-ttu-id="b71d5-225">Para dados transitórios que representam o estado de navegação, modele os dados como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="b71d5-225">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="b71d5-226">Exemplos de estado de usuário modelados na URL incluem:</span><span class="sxs-lookup"><span data-stu-id="b71d5-226">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="b71d5-227">A ID de uma entidade exibida.</span><span class="sxs-lookup"><span data-stu-id="b71d5-227">The ID of a viewed entity.</span></span>
* <span data-ttu-id="b71d5-228">O número da página atual em uma grade paginável.</span><span class="sxs-lookup"><span data-stu-id="b71d5-228">The current page number in a paged grid.</span></span>

<span data-ttu-id="b71d5-229">O conteúdo da barra de endereços do navegador é mantido:</span><span class="sxs-lookup"><span data-stu-id="b71d5-229">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="b71d5-230">Se o usuário recarregar a página manualmente.</span><span class="sxs-lookup"><span data-stu-id="b71d5-230">If the user manually reloads the page.</span></span>
* <span data-ttu-id="b71d5-231">Se o servidor Web ficar indisponível e o usuário for forçado a recarregar a página a fim de se conectar a um servidor diferente.</span><span class="sxs-lookup"><span data-stu-id="b71d5-231">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="b71d5-232">Para obter informações sobre como definir padrões de URL com a [`@page`](xref:mvc/views/razor#page) diretiva, consulte <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="b71d5-232">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

### <a name="browser-storage"></a><span data-ttu-id="b71d5-233">Armazenamento de navegador</span><span class="sxs-lookup"><span data-stu-id="b71d5-233">Browser storage</span></span>

<span data-ttu-id="b71d5-234">Para dados transitórios que o usuário está criando ativamente, um local de armazenamento usado normalmente é o navegador [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) e as [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) coleções:</span><span class="sxs-lookup"><span data-stu-id="b71d5-234">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="b71d5-235">`localStorage`está no escopo da janela do navegador.</span><span class="sxs-lookup"><span data-stu-id="b71d5-235">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="b71d5-236">Se o usuário recarregar a página ou fechar e abrir novamente o navegador, o estado persistirá.</span><span class="sxs-lookup"><span data-stu-id="b71d5-236">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="b71d5-237">Se o usuário abrir várias guias do navegador, o estado será compartilhado entre as guias.</span><span class="sxs-lookup"><span data-stu-id="b71d5-237">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="b71d5-238">Os dados persistem `localStorage` até serem explicitamente desmarcados.</span><span class="sxs-lookup"><span data-stu-id="b71d5-238">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="b71d5-239">`sessionStorage`está no escopo da guia navegador. Se o usuário recarregar a guia, o estado persiste.</span><span class="sxs-lookup"><span data-stu-id="b71d5-239">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="b71d5-240">Se o usuário fechar a guia ou o navegador, o estado será perdido.</span><span class="sxs-lookup"><span data-stu-id="b71d5-240">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="b71d5-241">Se o usuário abrir várias guias do navegador, cada guia terá sua própria versão independente dos dados.</span><span class="sxs-lookup"><span data-stu-id="b71d5-241">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="b71d5-242">Em geral, `sessionStorage` é mais seguro usar.</span><span class="sxs-lookup"><span data-stu-id="b71d5-242">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="b71d5-243">`sessionStorage`evita o risco de um usuário abrir várias guias e encontrar o seguinte:</span><span class="sxs-lookup"><span data-stu-id="b71d5-243">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="b71d5-244">Bugs no armazenamento de estado entre guias.</span><span class="sxs-lookup"><span data-stu-id="b71d5-244">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="b71d5-245">Comportamento confuso quando uma guia substitui o estado de outras guias.</span><span class="sxs-lookup"><span data-stu-id="b71d5-245">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="b71d5-246">`localStorage`é a melhor opção se o aplicativo precisar manter o estado entre fechar e abrir novamente o navegador.</span><span class="sxs-lookup"><span data-stu-id="b71d5-246">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="b71d5-247">Advertências para usar o armazenamento de navegador:</span><span class="sxs-lookup"><span data-stu-id="b71d5-247">Caveats for using browser storage:</span></span>

* <span data-ttu-id="b71d5-248">Assim como o uso de um banco de dados do lado do servidor, o carregamento e o salvamento do data são assíncronos.</span><span class="sxs-lookup"><span data-stu-id="b71d5-248">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="b71d5-249">Ao contrário de um banco de dados do lado do servidor, o armazenamento não está disponível durante o pré-processamento porque a página solicitada não existe no navegador durante o estágio de pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="b71d5-249">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="b71d5-250">O armazenamento de alguns kilobytes de dados é razoável para persistir para Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b71d5-250">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="b71d5-251">Além de alguns quilobytes, você deve considerar as implicações de desempenho porque os dados são carregados e salvos na rede.</span><span class="sxs-lookup"><span data-stu-id="b71d5-251">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="b71d5-252">Os usuários podem exibir ou adulterar os dados.</span><span class="sxs-lookup"><span data-stu-id="b71d5-252">Users may view or tamper with the data.</span></span> <span data-ttu-id="b71d5-253">[ASP.NET Core proteção de dados](xref:security/data-protection/introduction) pode reduzir o risco.</span><span class="sxs-lookup"><span data-stu-id="b71d5-253">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="b71d5-254">Por exemplo, [ASP.NET Core armazenamento de navegador protegido](#aspnet-core-protected-browser-storage) usa a proteção de dados do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b71d5-254">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="b71d5-255">Os pacotes NuGet de terceiros fornecem APIs para trabalhar com o `localStorage` e o `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="b71d5-255">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="b71d5-256">Vale a pena considerar a escolha de um pacote que use de modo transparente [ASP.NET Core proteção de dados](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="b71d5-256">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="b71d5-257">A proteção de dados criptografa os dados armazenados e reduz o risco potencial de adulterar os dados armazenados.</span><span class="sxs-lookup"><span data-stu-id="b71d5-257">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="b71d5-258">Se os dados serializados em JSON forem armazenados em texto sem formatação, os usuários poderão ver os dados usando as ferramentas de desenvolvedor do navegador e também modificar os dados armazenados.</span><span class="sxs-lookup"><span data-stu-id="b71d5-258">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="b71d5-259">Proteger os dados nem sempre é um problema porque os dados podem ser triviais por natureza.</span><span class="sxs-lookup"><span data-stu-id="b71d5-259">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="b71d5-260">Por exemplo, ler ou modificar a cor armazenada de um elemento de interface do usuário não é um risco de segurança significativo para o usuário ou a organização.</span><span class="sxs-lookup"><span data-stu-id="b71d5-260">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="b71d5-261">Evite permitir que os usuários inspecionem ou violem *dados confidenciais*.</span><span class="sxs-lookup"><span data-stu-id="b71d5-261">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="b71d5-262">ASP.NET Core o armazenamento de navegador protegido</span><span class="sxs-lookup"><span data-stu-id="b71d5-262">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="b71d5-263">ASP.NET Core armazenamento de navegador protegido aproveita a [proteção de dados do ASP.NET Core](xref:security/data-protection/introduction) para o e o [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="b71d5-263">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="b71d5-264">O armazenamento de navegador protegido depende do ASP.NET Core proteção de dados e tem suporte apenas para Blazor Server aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b71d5-264">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="b71d5-265">Configuração</span><span class="sxs-lookup"><span data-stu-id="b71d5-265">Configuration</span></span>

1. <span data-ttu-id="b71d5-266">Adicione uma referência de pacote a [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions) .</span><span class="sxs-lookup"><span data-stu-id="b71d5-266">Add a package reference to [`Microsoft.AspNetCore.Components.Web.Extensions`](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions).</span></span>
1. <span data-ttu-id="b71d5-267">No `Startup.ConfigureServices` , chame `AddProtectedBrowserStorage` para adicionar `localStorage` e `sessionStorage` serviços à coleção de serviços:</span><span class="sxs-lookup"><span data-stu-id="b71d5-267">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="b71d5-268">Salvar e carregar dados dentro de um componente</span><span class="sxs-lookup"><span data-stu-id="b71d5-268">Save and load data within a component</span></span>

<span data-ttu-id="b71d5-269">Em qualquer componente que exija carregar ou salvar dados no armazenamento de navegador, use a [`@inject`](xref:mvc/views/razor#inject) diretiva para injetar uma instância de um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="b71d5-269">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="b71d5-270">A escolha depende de qual local de armazenamento do navegador você deseja usar.</span><span class="sxs-lookup"><span data-stu-id="b71d5-270">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="b71d5-271">No exemplo a seguir, `sessionStorage` é usado:</span><span class="sxs-lookup"><span data-stu-id="b71d5-271">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="b71d5-272">A `@using` diretiva pode ser colocada no arquivo do aplicativo `_Imports.razor` , em vez de no componente.</span><span class="sxs-lookup"><span data-stu-id="b71d5-272">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="b71d5-273">O uso do `_Imports.razor` arquivo disponibiliza o namespace para segmentos maiores do aplicativo ou todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b71d5-273">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="b71d5-274">Para persistir o `currentCount` valor no `Counter` componente de um aplicativo com base no Blazor Server modelo de projeto, modifique o `IncrementCount` método a ser usado `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="b71d5-274">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="b71d5-275">Em aplicativos maiores e mais realistas, o armazenamento de campos individuais é um cenário improvável.</span><span class="sxs-lookup"><span data-stu-id="b71d5-275">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="b71d5-276">Os aplicativos têm maior probabilidade de armazenar objetos de modelo inteiros que incluem estado complexo.</span><span class="sxs-lookup"><span data-stu-id="b71d5-276">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="b71d5-277">`ProtectedSessionStore`o serializa e desserializa automaticamente os dados JSON para armazenar objetos de estado complexo.</span><span class="sxs-lookup"><span data-stu-id="b71d5-277">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="b71d5-278">No exemplo de código anterior, os `currentCount` dados são armazenados como `sessionStorage['count']` no navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="b71d5-278">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="b71d5-279">Os dados não são armazenados em texto sem formatação, mas, em vez disso, são protegidos usando ASP.NET Core proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="b71d5-279">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="b71d5-280">Os dados criptografados poderão ser inspecionados se `sessionStorage['count']` o for avaliado no console do desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="b71d5-280">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="b71d5-281">Para recuperar os `currentCount` dados, se o usuário retornar ao `Counter` componente posteriormente, incluindo se o usuário está em um novo circuito, use `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="b71d5-281">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="b71d5-282">Se os parâmetros do componente incluírem o estado de navegação, chame `ProtectedSessionStore.GetAsync` e atribua um não `null` resultado <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , não <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="b71d5-282">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="b71d5-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>é chamado apenas uma vez quando o componente é instanciado pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="b71d5-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="b71d5-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>Não será chamado novamente mais tarde se o usuário navegar para uma URL diferente enquanto permanece na mesma página.</span><span class="sxs-lookup"><span data-stu-id="b71d5-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="b71d5-285">Para obter mais informações, consulte <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="b71d5-285">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="b71d5-286">Os exemplos nesta seção só funcionarão se o servidor não tiver o pré-processamento habilitado.</span><span class="sxs-lookup"><span data-stu-id="b71d5-286">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="b71d5-287">Com o pré-processamento habilitado, um erro é gerado explicando que as chamadas de interoperabilidade do JavaScript não podem ser emitidas porque o componente está sendo renderizado.</span><span class="sxs-lookup"><span data-stu-id="b71d5-287">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="b71d5-288">Desabilite o pré-processamento ou adicione código adicional para trabalhar com o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="b71d5-288">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="b71d5-289">Para saber mais sobre como escrever código que funciona com o pré-processamento, consulte a seção [manipular pré-processamento](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="b71d5-289">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="b71d5-290">Manipular o estado de carregamento</span><span class="sxs-lookup"><span data-stu-id="b71d5-290">Handle the loading state</span></span>

<span data-ttu-id="b71d5-291">Como o armazenamento de navegador é acessado de forma assíncrona em uma conexão de rede, sempre há um período de tempo antes que os dados sejam carregados e disponibilizados para um componente.</span><span class="sxs-lookup"><span data-stu-id="b71d5-291">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="b71d5-292">Para obter os melhores resultados, processe uma mensagem de estado de carregamento durante o carregamento em andamento, em vez de exibir dados em branco ou padrão.</span><span class="sxs-lookup"><span data-stu-id="b71d5-292">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="b71d5-293">Uma abordagem é controlar se os dados são `null` , o que significa que os dados ainda estão sendo carregados.</span><span class="sxs-lookup"><span data-stu-id="b71d5-293">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="b71d5-294">No componente padrão `Counter` , a contagem é mantida em um `int` .</span><span class="sxs-lookup"><span data-stu-id="b71d5-294">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="b71d5-295">[Tornar `currentCount` anulável](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) adicionando um ponto de interrogação ( `?` ) ao tipo ( `int` ):</span><span class="sxs-lookup"><span data-stu-id="b71d5-295">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="b71d5-296">Em vez de exibir incondicionalmente a contagem e o **`Increment`** botão, exiba esses elementos somente se os dados forem carregados por meio da verificação <xref:System.Nullable%601.HasValue%2A> :</span><span class="sxs-lookup"><span data-stu-id="b71d5-296">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="b71d5-297">Processar pré-processamento</span><span class="sxs-lookup"><span data-stu-id="b71d5-297">Handle prerendering</span></span>

<span data-ttu-id="b71d5-298">Durante o pré-processamento:</span><span class="sxs-lookup"><span data-stu-id="b71d5-298">During prerendering:</span></span>

* <span data-ttu-id="b71d5-299">Não existe uma conexão interativa com o navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="b71d5-299">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="b71d5-300">O navegador ainda não tem uma página na qual possa executar código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b71d5-300">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="b71d5-301">`localStorage`ou `sessionStorage` não estão disponíveis durante o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="b71d5-301">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="b71d5-302">Se o componente tentar interagir com o armazenamento, um erro será gerado explicando que as chamadas de interoperabilidade do JavaScript não podem ser emitidas porque o componente está sendo renderizado.</span><span class="sxs-lookup"><span data-stu-id="b71d5-302">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="b71d5-303">Uma maneira de resolver o erro é desabilitar o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="b71d5-303">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="b71d5-304">Normalmente, essa é a melhor opção se o aplicativo fizer uso intensivo de armazenamento baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="b71d5-304">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="b71d5-305">O pré-processamento adiciona complexidade e não beneficia o aplicativo porque o aplicativo não pode colocar nenhum conteúdo útil até `localStorage` ou `sessionStorage` estar disponível.</span><span class="sxs-lookup"><span data-stu-id="b71d5-305">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="b71d5-306">Para desabilitar o pré-processamento, abra o `Pages/_Host.cshtml` arquivo e altere o `render-mode` atributo do auxiliar de [marca de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="b71d5-306">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="b71d5-307">O pré-processamento pode ser útil para outras páginas que não usam o `localStorage` ou o `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="b71d5-307">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="b71d5-308">Para reter o pré-processamento, adie a operação de carregamento até que o navegador esteja conectado ao circuito.</span><span class="sxs-lookup"><span data-stu-id="b71d5-308">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="b71d5-309">Veja a seguir um exemplo de como armazenar um valor de contador:</span><span class="sxs-lookup"><span data-stu-id="b71d5-309">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="b71d5-310">Fatorar a preservação do estado para um local comum</span><span class="sxs-lookup"><span data-stu-id="b71d5-310">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="b71d5-311">Se muitos componentes dependem do armazenamento baseado em navegador, a nova implementação do código do provedor de estado muitas vezes cria a duplicação de código.</span><span class="sxs-lookup"><span data-stu-id="b71d5-311">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="b71d5-312">Uma opção para evitar a duplicação de código é criar um *componente pai do provedor de estado* que encapsula a lógica do provedor de estado.</span><span class="sxs-lookup"><span data-stu-id="b71d5-312">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="b71d5-313">Os componentes filho podem trabalhar com dados persistentes sem considerar o mecanismo de persistência de estado.</span><span class="sxs-lookup"><span data-stu-id="b71d5-313">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="b71d5-314">No exemplo a seguir de um `CounterStateProvider` componente, os dados do contador são persistidos para `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="b71d5-314">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="b71d5-315">O `CounterStateProvider` componente manipula a fase de carregamento não processando seu conteúdo filho até que o carregamento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="b71d5-315">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="b71d5-316">Para usar o `CounterStateProvider` componente, empacote uma instância do componente em qualquer outro componente que exija acesso ao estado do contador.</span><span class="sxs-lookup"><span data-stu-id="b71d5-316">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="b71d5-317">Para tornar o estado acessível a todos os componentes em um aplicativo, empacote o `CounterStateProvider` componente em volta do <xref:Microsoft.AspNetCore.Components.Routing.Router> no `App` componente ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="b71d5-317">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="b71d5-318">Os componentes encapsulados recebem e podem modificar o estado do contador persistente.</span><span class="sxs-lookup"><span data-stu-id="b71d5-318">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="b71d5-319">O componente a seguir `Counter` implementa o padrão:</span><span class="sxs-lookup"><span data-stu-id="b71d5-319">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="b71d5-320">O componente anterior não precisa interagir com o `ProtectedBrowserStorage` , nem lidar com uma fase de "carregamento".</span><span class="sxs-lookup"><span data-stu-id="b71d5-320">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="b71d5-321">Para lidar com o pré-processamento conforme descrito anteriormente, `CounterStateProvider` pode ser alterado para que todos os componentes que consumam os dados do contador funcionem automaticamente com o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="b71d5-321">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="b71d5-322">Para obter mais informações, consulte a seção [manipular pré-processamento](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="b71d5-322">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="b71d5-323">Em geral, o padrão de *componente pai do provedor de estado* é recomendado:</span><span class="sxs-lookup"><span data-stu-id="b71d5-323">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="b71d5-324">Para consumir o estado em vários componentes.</span><span class="sxs-lookup"><span data-stu-id="b71d5-324">To consume state across many components.</span></span>
* <span data-ttu-id="b71d5-325">Se houver apenas um objeto de estado de nível superior para persistir.</span><span class="sxs-lookup"><span data-stu-id="b71d5-325">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="b71d5-326">Para persistir muitos objetos de estado diferentes e consumir diferentes subconjuntos de objetos em locais diferentes, é melhor evitar o estado de persistência globalmente.</span><span class="sxs-lookup"><span data-stu-id="b71d5-326">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="b71d5-327">Pacote NuGet experimental do armazenamento de navegador protegido</span><span class="sxs-lookup"><span data-stu-id="b71d5-327">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="b71d5-328">ASP.NET Core armazenamento de navegador protegido aproveita a [proteção de dados do ASP.NET Core](xref:security/data-protection/introduction) para o e o [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="b71d5-328">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="b71d5-329">`Microsoft.AspNetCore.ProtectedBrowserStorage`é um pacote experimental sem suporte não adequado para uso em produção.</span><span class="sxs-lookup"><span data-stu-id="b71d5-329">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="b71d5-330">O pacote só está disponível para uso em aplicativos ASP.NET Core 3,1 Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="b71d5-330">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="b71d5-331">Configuração</span><span class="sxs-lookup"><span data-stu-id="b71d5-331">Configuration</span></span>

1. <span data-ttu-id="b71d5-332">Adicione uma referência de pacote a [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .</span><span class="sxs-lookup"><span data-stu-id="b71d5-332">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="b71d5-333">No `Pages/_Host.cshtml` arquivo, adicione o seguinte script dentro da marca de fechamento `</body>` :</span><span class="sxs-lookup"><span data-stu-id="b71d5-333">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="b71d5-334">No `Startup.ConfigureServices` , chame `AddProtectedBrowserStorage` para adicionar `localStorage` e `sessionStorage` serviços à coleção de serviços:</span><span class="sxs-lookup"><span data-stu-id="b71d5-334">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="b71d5-335">Salvar e carregar dados dentro de um componente</span><span class="sxs-lookup"><span data-stu-id="b71d5-335">Save and load data within a component</span></span>

<span data-ttu-id="b71d5-336">Em qualquer componente que exija carregar ou salvar dados no armazenamento de navegador, use a [`@inject`](xref:mvc/views/razor#inject) diretiva para injetar uma instância de um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="b71d5-336">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="b71d5-337">A escolha depende de qual local de armazenamento do navegador você deseja usar.</span><span class="sxs-lookup"><span data-stu-id="b71d5-337">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="b71d5-338">No exemplo a seguir, `sessionStorage` é usado:</span><span class="sxs-lookup"><span data-stu-id="b71d5-338">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="b71d5-339">A `@using` instrução pode ser colocada em um `_Imports.razor` arquivo em vez de no componente.</span><span class="sxs-lookup"><span data-stu-id="b71d5-339">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="b71d5-340">O uso do `_Imports.razor` arquivo disponibiliza o namespace para segmentos maiores do aplicativo ou todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b71d5-340">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="b71d5-341">Para persistir o `currentCount` valor no `Counter` componente de um aplicativo com base no Blazor Server modelo de projeto, modifique o `IncrementCount` método a ser usado `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="b71d5-341">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="b71d5-342">Em aplicativos maiores e mais realistas, o armazenamento de campos individuais é um cenário improvável.</span><span class="sxs-lookup"><span data-stu-id="b71d5-342">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="b71d5-343">Os aplicativos têm maior probabilidade de armazenar objetos de modelo inteiros que incluem estado complexo.</span><span class="sxs-lookup"><span data-stu-id="b71d5-343">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="b71d5-344">`ProtectedSessionStore`o serializa e desserializa automaticamente os dados JSON.</span><span class="sxs-lookup"><span data-stu-id="b71d5-344">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="b71d5-345">No exemplo de código anterior, os `currentCount` dados são armazenados como `sessionStorage['count']` no navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="b71d5-345">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="b71d5-346">Os dados não são armazenados em texto sem formatação, mas, em vez disso, são protegidos usando ASP.NET Core proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="b71d5-346">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="b71d5-347">Os dados criptografados poderão ser inspecionados se `sessionStorage['count']` o for avaliado no console do desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="b71d5-347">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="b71d5-348">Para recuperar os `currentCount` dados, se o usuário retornar ao `Counter` componente posteriormente, incluindo se eles estão em um circuito totalmente novo, use `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="b71d5-348">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="b71d5-349">Se os parâmetros do componente incluírem o estado de navegação, chame `ProtectedSessionStore.GetAsync` e atribua o resultado em <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , não <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="b71d5-349">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="b71d5-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>é chamado apenas uma vez quando o componente é instanciado pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="b71d5-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="b71d5-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>Não será chamado novamente mais tarde se o usuário navegar para uma URL diferente enquanto permanece na mesma página.</span><span class="sxs-lookup"><span data-stu-id="b71d5-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="b71d5-352">Para obter mais informações, consulte <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="b71d5-352">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="b71d5-353">Os exemplos nesta seção só funcionarão se o servidor não tiver o pré-processamento habilitado.</span><span class="sxs-lookup"><span data-stu-id="b71d5-353">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="b71d5-354">Com o pré-processamento habilitado, um erro é gerado explicando que as chamadas de interoperabilidade do JavaScript não podem ser emitidas porque o componente está sendo renderizado.</span><span class="sxs-lookup"><span data-stu-id="b71d5-354">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="b71d5-355">Desabilite o pré-processamento ou adicione código adicional para trabalhar com o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="b71d5-355">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="b71d5-356">Para saber mais sobre como escrever código que funciona com o pré-processamento, consulte a seção [manipular pré-processamento](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="b71d5-356">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="b71d5-357">Manipular o estado de carregamento</span><span class="sxs-lookup"><span data-stu-id="b71d5-357">Handle the loading state</span></span>

<span data-ttu-id="b71d5-358">Como o armazenamento de navegador é acessado de forma assíncrona em uma conexão de rede, sempre há um período de tempo antes que os dados sejam carregados e disponibilizados para um componente.</span><span class="sxs-lookup"><span data-stu-id="b71d5-358">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="b71d5-359">Para obter os melhores resultados, processe uma mensagem de estado de carregamento durante o carregamento em andamento, em vez de exibir dados em branco ou padrão.</span><span class="sxs-lookup"><span data-stu-id="b71d5-359">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="b71d5-360">Uma abordagem é controlar se os dados são `null` , o que significa que os dados ainda estão sendo carregados.</span><span class="sxs-lookup"><span data-stu-id="b71d5-360">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="b71d5-361">No componente padrão `Counter` , a contagem é mantida em um `int` .</span><span class="sxs-lookup"><span data-stu-id="b71d5-361">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="b71d5-362">[Tornar `currentCount` anulável](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) adicionando um ponto de interrogação ( `?` ) ao tipo ( `int` ):</span><span class="sxs-lookup"><span data-stu-id="b71d5-362">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="b71d5-363">Em vez de exibir incondicionalmente a contagem e o **`Increment`** botão, escolha exibir esses elementos somente se os dados forem carregados:</span><span class="sxs-lookup"><span data-stu-id="b71d5-363">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="b71d5-364">Processar pré-processamento</span><span class="sxs-lookup"><span data-stu-id="b71d5-364">Handle prerendering</span></span>

<span data-ttu-id="b71d5-365">Durante o pré-processamento:</span><span class="sxs-lookup"><span data-stu-id="b71d5-365">During prerendering:</span></span>

* <span data-ttu-id="b71d5-366">Não existe uma conexão interativa com o navegador do usuário.</span><span class="sxs-lookup"><span data-stu-id="b71d5-366">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="b71d5-367">O navegador ainda não tem uma página na qual possa executar código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b71d5-367">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="b71d5-368">`localStorage`ou `sessionStorage` não estão disponíveis durante o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="b71d5-368">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="b71d5-369">Se o componente tentar interagir com o armazenamento, um erro será gerado explicando que as chamadas de interoperabilidade do JavaScript não podem ser emitidas porque o componente está sendo renderizado.</span><span class="sxs-lookup"><span data-stu-id="b71d5-369">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="b71d5-370">Uma maneira de resolver o erro é desabilitar o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="b71d5-370">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="b71d5-371">Normalmente, essa é a melhor opção se o aplicativo fizer uso intensivo de armazenamento baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="b71d5-371">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="b71d5-372">O pré-processamento adiciona complexidade e não beneficia o aplicativo porque o aplicativo não pode colocar nenhum conteúdo útil até `localStorage` ou `sessionStorage` estar disponível.</span><span class="sxs-lookup"><span data-stu-id="b71d5-372">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="b71d5-373">Para desabilitar o pré-processamento, abra o `Pages/_Host.cshtml` arquivo e altere o `render-mode` atributo do auxiliar de [marca de componente](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) para <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="b71d5-373">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="b71d5-374">O pré-processamento pode ser útil para outras páginas que não usam o `localStorage` ou o `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="b71d5-374">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="b71d5-375">Para reter o pré-processamento, adie a operação de carregamento até que o navegador esteja conectado ao circuito.</span><span class="sxs-lookup"><span data-stu-id="b71d5-375">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="b71d5-376">Veja a seguir um exemplo de como armazenar um valor de contador:</span><span class="sxs-lookup"><span data-stu-id="b71d5-376">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="b71d5-377">Fatorar a preservação do estado para um local comum</span><span class="sxs-lookup"><span data-stu-id="b71d5-377">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="b71d5-378">Se muitos componentes dependem do armazenamento baseado em navegador, a nova implementação do código do provedor de estado muitas vezes cria a duplicação de código.</span><span class="sxs-lookup"><span data-stu-id="b71d5-378">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="b71d5-379">Uma opção para evitar a duplicação de código é criar um *componente pai do provedor de estado* que encapsula a lógica do provedor de estado.</span><span class="sxs-lookup"><span data-stu-id="b71d5-379">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="b71d5-380">Os componentes filho podem trabalhar com dados persistentes sem considerar o mecanismo de persistência de estado.</span><span class="sxs-lookup"><span data-stu-id="b71d5-380">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="b71d5-381">No exemplo a seguir de um `CounterStateProvider` componente, os dados do contador são persistidos para `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="b71d5-381">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="b71d5-382">O `CounterStateProvider` componente manipula a fase de carregamento não processando seu conteúdo filho até que o carregamento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="b71d5-382">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="b71d5-383">Para usar o `CounterStateProvider` componente, empacote uma instância do componente em qualquer outro componente que exija acesso ao estado do contador.</span><span class="sxs-lookup"><span data-stu-id="b71d5-383">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="b71d5-384">Para tornar o estado acessível a todos os componentes em um aplicativo, empacote o `CounterStateProvider` componente em volta do <xref:Microsoft.AspNetCore.Components.Routing.Router> no `App` componente ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="b71d5-384">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="b71d5-385">Os componentes encapsulados recebem e podem modificar o estado do contador persistente.</span><span class="sxs-lookup"><span data-stu-id="b71d5-385">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="b71d5-386">O componente a seguir `Counter` implementa o padrão:</span><span class="sxs-lookup"><span data-stu-id="b71d5-386">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="b71d5-387">O componente anterior não precisa interagir com o `ProtectedBrowserStorage` , nem lidar com uma fase de "carregamento".</span><span class="sxs-lookup"><span data-stu-id="b71d5-387">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="b71d5-388">Para lidar com o pré-processamento conforme descrito anteriormente, `CounterStateProvider` pode ser alterado para que todos os componentes que consumam os dados do contador funcionem automaticamente com o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="b71d5-388">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="b71d5-389">Para obter mais informações, consulte a seção [manipular pré-processamento](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="b71d5-389">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="b71d5-390">Em geral, o padrão de *componente pai do provedor de estado* é recomendado:</span><span class="sxs-lookup"><span data-stu-id="b71d5-390">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="b71d5-391">Para consumir o estado em vários componentes.</span><span class="sxs-lookup"><span data-stu-id="b71d5-391">To consume state across many components.</span></span>
* <span data-ttu-id="b71d5-392">Se houver apenas um objeto de estado de nível superior para persistir.</span><span class="sxs-lookup"><span data-stu-id="b71d5-392">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="b71d5-393">Para persistir muitos objetos de estado diferentes e consumir diferentes subconjuntos de objetos em locais diferentes, é melhor evitar o estado de persistência globalmente.</span><span class="sxs-lookup"><span data-stu-id="b71d5-393">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: zone-end
