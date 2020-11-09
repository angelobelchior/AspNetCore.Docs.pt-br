---
title: Solicitar recursos no ASP.NET Core
author: ardalis
description: Saiba mais sobre detalhes de implementação de servidor Web relacionados a solicitações HTTP e as respostas que são definidas em interfaces para o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: fundamentals/request-features
ms.openlocfilehash: d906474b0fd291cc4a68390f390b2bf538e21eff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053703"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="36109-103">Solicitar recursos no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36109-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="36109-104">Por [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="36109-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="36109-105">A `HttpContext` API que os aplicativos e o middleware usam para processar solicitações tem uma camada de abstração undernieth ela chamou *interfaces de recurso* .</span><span class="sxs-lookup"><span data-stu-id="36109-105">The `HttpContext` API that applications and middleware use to process requests has an abstraction layer undernieth it called *feature interfaces* .</span></span> <span data-ttu-id="36109-106">Cada interface de recurso fornece um subconjunto granular da funcionalidade exposta pelo `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="36109-106">Each feature interface provides a granular subset of the functionality exposed by `HttpContext`.</span></span> <span data-ttu-id="36109-107">Essas interfaces podem ser adicionadas, modificadas, encapsuladas, substituídas ou até mesmo removidas pelo servidor ou middleware, pois a solicitação é processada sem a necessidade de reimplementar todo o processo `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="36109-107">These interfaces can be added, modified, wrapped, replaced, or even removed by the server or middleware as the request is processed without having to re-implement the entire `HttpContext`.</span></span> <span data-ttu-id="36109-108">Eles também podem ser usados para simular a funcionalidade durante o teste.</span><span class="sxs-lookup"><span data-stu-id="36109-108">They can also be used to mock functionality when testing.</span></span>

## <a name="feature-collections"></a><span data-ttu-id="36109-109">Coleções de recursos</span><span class="sxs-lookup"><span data-stu-id="36109-109">Feature collections</span></span>

<span data-ttu-id="36109-110">A <xref:Microsoft.AspNetCore.Http.HttpContext.Features> propriedade de `HttpContext` fornece acesso à coleção de interfaces de recurso para a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="36109-110">The <xref:Microsoft.AspNetCore.Http.HttpContext.Features> property of `HttpContext` provides access to the collection of feature interfaces for the current request.</span></span> <span data-ttu-id="36109-111">Como a coleção de recursos é mutável, mesmo no contexto de uma solicitação, o middleware pode ser usado para modificar a coleção e adicionar suporte para recursos adicionais.</span><span class="sxs-lookup"><span data-stu-id="36109-111">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span> <span data-ttu-id="36109-112">Alguns recursos avançados só estão disponíveis acessando a interface associada por meio da coleção de recursos.</span><span class="sxs-lookup"><span data-stu-id="36109-112">Some advanced features are only available by accessing the associated interface through the feature collection.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="36109-113">Interfaces de recurso</span><span class="sxs-lookup"><span data-stu-id="36109-113">Feature interfaces</span></span>

<span data-ttu-id="36109-114">ASP.NET Core define um número de interfaces de recurso HTTP comuns no <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> , que são compartilhadas por vários servidores e middleware para identificar os recursos aos quais dão suporte.</span><span class="sxs-lookup"><span data-stu-id="36109-114">ASP.NET Core defines a number of common HTTP feature interfaces in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>, which are shared by various servers and middleware to identify the features that they support.</span></span> <span data-ttu-id="36109-115">Os servidores e o middleware também podem fornecer suas próprias interfaces com funcionalidade adicional.</span><span class="sxs-lookup"><span data-stu-id="36109-115">Servers and middleware may also provide their own interfaces with additional functionality.</span></span>

<span data-ttu-id="36109-116">A maioria das interfaces de recursos fornecerá funcionalidade opcional, de iluminação e suas `HttpContext` APIs associadas fornecerão padrões se o recurso não estiver presente.</span><span class="sxs-lookup"><span data-stu-id="36109-116">Most feature interfaces provide optional, light-up functionality, and their associated `HttpContext` APIs provide defaults if the feature isn't present.</span></span> <span data-ttu-id="36109-117">Algumas interfaces são indicadas no conteúdo a seguir, conforme necessário, pois elas fornecem a funcionalidade principal de solicitação e resposta e devem ser implementadas para processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="36109-117">A few interfaces are indicated in the following content as required because they provide core request and response functionality and must be implemented in order to process the request.</span></span>

<span data-ttu-id="36109-118">As seguintes interfaces de recurso são de <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="36109-118">The following feature interfaces are from <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:</span></span>

<span data-ttu-id="36109-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Define a estrutura de uma solicitação HTTP, incluindo o protocolo, o caminho, a cadeia de caracteres de consulta, os cabeçalhos e o corpo.</span><span class="sxs-lookup"><span data-stu-id="36109-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span> <span data-ttu-id="36109-120">Esse recurso é necessário para processar solicitações.</span><span class="sxs-lookup"><span data-stu-id="36109-120">This feature is required in order to process requests.</span></span>

<span data-ttu-id="36109-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Define a estrutura de uma resposta HTTP, incluindo o código de status, os cabeçalhos e o corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="36109-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span> <span data-ttu-id="36109-122">Esse recurso é necessário para processar solicitações.</span><span class="sxs-lookup"><span data-stu-id="36109-122">This feature is required in order to process requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36109-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Define diferentes maneiras de gravar o corpo da resposta, usando um `Stream` , um `PipeWriter` ou um arquivo.</span><span class="sxs-lookup"><span data-stu-id="36109-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Defines different ways of writing out the response body, using either a `Stream`, a `PipeWriter`, or a file.</span></span> <span data-ttu-id="36109-124">Esse recurso é necessário para processar solicitações.</span><span class="sxs-lookup"><span data-stu-id="36109-124">This feature is required in order to process requests.</span></span> <span data-ttu-id="36109-125">Isso substitui `IHttpResponseFeature.Body` e `IHttpSendFileFeature` .</span><span class="sxs-lookup"><span data-stu-id="36109-125">This replaces `IHttpResponseFeature.Body` and `IHttpSendFileFeature`.</span></span>

::: moniker-end

<span data-ttu-id="36109-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Mantém o <xref:System.Security.Claims.ClaimsPrincipal> associado à solicitação no momento.</span><span class="sxs-lookup"><span data-stu-id="36109-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Holds the <xref:System.Security.Claims.ClaimsPrincipal> currently associated with the request.</span></span>

<span data-ttu-id="36109-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Usado para analisar e armazenar em cache os envios de formulários de entrada de várias partes e HTTP.</span><span class="sxs-lookup"><span data-stu-id="36109-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Used to parse and cache incoming HTTP and multipart form submissions.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="36109-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Usado para controlar se as operações de e/s síncronas são permitidas para os corpos de solicitação ou resposta.</span><span class="sxs-lookup"><span data-stu-id="36109-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Used to control if synchronous IO operations are allowed for the request or response bodies.</span></span>

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="36109-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Define métodos para desabilitar o buffer de solicitações e/ou respostas.</span><span class="sxs-lookup"><span data-stu-id="36109-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Defines methods for disabling buffering of requests and/or responses.</span></span>

::: moniker-end

<span data-ttu-id="36109-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Define propriedades para a ID de conexão e endereços locais e remotos e portas.</span><span class="sxs-lookup"><span data-stu-id="36109-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Defines properties for the connection id and local and remote addresses and ports.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="36109-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controla o tamanho máximo de corpo de solicitação permitido para a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="36109-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controls the maximum allowed request body size for the current request.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="36109-132">`IHttpRequestBodyDetectionFeature`: Indica se a solicitação pode ter um corpo.</span><span class="sxs-lookup"><span data-stu-id="36109-132">`IHttpRequestBodyDetectionFeature`: Indicates if the request can have a body.</span></span>

::: moniker-end

<span data-ttu-id="36109-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Adiciona uma propriedade que pode ser implementada para identificar solicitações exclusivamente.</span><span class="sxs-lookup"><span data-stu-id="36109-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="36109-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Define o suporte para anular conexões ou detectar se uma solicitação foi encerrada prematuramente, como por uma desconexão do cliente.</span><span class="sxs-lookup"><span data-stu-id="36109-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Defines support for aborting connections or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36109-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Fornece acesso aos cabeçalhos de trailer de solicitação, se houver.</span><span class="sxs-lookup"><span data-stu-id="36109-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Provides access to the request trailer headers, if any.</span></span>

<span data-ttu-id="36109-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Usado para enviar mensagens de redefinição para protocolos que dão suporte a eles como HTTP/2 ou HTTP/3.</span><span class="sxs-lookup"><span data-stu-id="36109-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Used to send reset messages for protocols that support them such as HTTP/2 or HTTP/3.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="36109-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Permite que o aplicativo forneça cabeçalhos de trailer de resposta, se houver suporte.</span><span class="sxs-lookup"><span data-stu-id="36109-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Enables the application to provide response trailer headers if supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="36109-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Define um método para enviar arquivos de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="36109-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Defines a method for sending files asynchronously.</span></span>

::: moniker-end

<span data-ttu-id="36109-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Define o suporte para [atualizações http](https://tools.ietf.org/html/rfc2616.html#section-14.42), que permitem ao cliente especificar quais protocolos adicionais ele gostaria de usar se o servidor quiser alternar protocolos.</span><span class="sxs-lookup"><span data-stu-id="36109-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="36109-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Define uma API para dar suporte a soquetes da Web.</span><span class="sxs-lookup"><span data-stu-id="36109-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Defines an API for supporting web sockets.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36109-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controla se a compactação de resposta deve ser usada em conexões HTTPS.</span><span class="sxs-lookup"><span data-stu-id="36109-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controls if response compression should be used over HTTPS connections.</span></span>

::: moniker-end

<span data-ttu-id="36109-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Armazena a <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> coleção para o estado do aplicativo por solicitação.</span><span class="sxs-lookup"><span data-stu-id="36109-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Stores the <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> collection for per request application state.</span></span>

<span data-ttu-id="36109-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Analisa e armazena em cache a cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="36109-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Parses and caches the query string.</span></span>
   
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36109-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Representa o corpo da solicitação como um <xref:System.IO.Pipelines.PipeReader> .</span><span class="sxs-lookup"><span data-stu-id="36109-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Represents the request body as a <xref:System.IO.Pipelines.PipeReader>.</span></span>
 
::: moniker-end

<span data-ttu-id="36109-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Analisa e armazena em cache os valores de cabeçalho da solicitação `Cookie` .</span><span class="sxs-lookup"><span data-stu-id="36109-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Parses and caches the request `Cookie` header values.</span></span>

<span data-ttu-id="36109-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Controla como cookie as respostas s são aplicadas ao `Set-Cookie` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="36109-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Controls how response cookies are applied to the `Set-Cookie` header.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="36109-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: Esse recurso fornece acesso a variáveis de servidor de solicitação, como as fornecidas pelo IIS.</span><span class="sxs-lookup"><span data-stu-id="36109-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: This feature provides access to request server variables such as those provided by IIS.</span></span>

::: moniker-end
   
<span data-ttu-id="36109-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Fornece acesso a um <xref:System.IServiceProvider> com serviços de solicitação com escopo.</span><span class="sxs-lookup"><span data-stu-id="36109-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Provides access to an <xref:System.IServiceProvider> with scoped request services.</span></span>

<span data-ttu-id="36109-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Define `ISessionFactory` e <xref:Microsoft.AspNetCore.Http.ISession> abstrações para dar suporte a sessões de usuário.</span><span class="sxs-lookup"><span data-stu-id="36109-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Defines `ISessionFactory` and <xref:Microsoft.AspNetCore.Http.ISession> abstractions for supporting user sessions.</span></span> <span data-ttu-id="36109-150">`ISessionFeature` é implementado pelo <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (consulte <xref:fundamentals/app-state> ).</span><span class="sxs-lookup"><span data-stu-id="36109-150">`ISessionFeature` is implemented by the <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (see <xref:fundamentals/app-state>).</span></span>

<span data-ttu-id="36109-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Define uma API para recuperar certificados de cliente.</span><span class="sxs-lookup"><span data-stu-id="36109-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="36109-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Define métodos para trabalhar com parâmetros de associação de token TLS.</span><span class="sxs-lookup"><span data-stu-id="36109-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Defines methods for working with TLS token binding parameters.</span></span>
   
::: moniker range=">= aspnetcore-2.2"
   
<span data-ttu-id="36109-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Usado para consultar, conceder e retirar o consentimento do usuário em relação ao armazenamento de informações do usuário relacionadas à atividade e à funcionalidade do site.</span><span class="sxs-lookup"><span data-stu-id="36109-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Used to query, grant, and withdraw user consent regarding the storage of user information related to site activity and functionality.</span></span>
   
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="36109-154">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="36109-154">Additional resources</span></span>

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
