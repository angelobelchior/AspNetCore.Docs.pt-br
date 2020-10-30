---
title: Solicitar recursos no ASP.NET Core
author: ardalis
description: Saiba mais sobre detalhes de implementação de servidor Web relacionados a solicitações HTTP e as respostas que são definidas em interfaces para o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
- appsettings.json
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
uid: fundamentals/request-features
ms.openlocfilehash: d906474b0fd291cc4a68390f390b2bf538e21eff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053703"
---
# <a name="request-features-in-aspnet-core"></a>Solicitar recursos no ASP.NET Core

Por [Steve Smith](https://ardalis.com/)

A `HttpContext` API que os aplicativos e o middleware usam para processar solicitações tem uma camada de abstração undernieth ela chamou *interfaces de recurso* . Cada interface de recurso fornece um subconjunto granular da funcionalidade exposta pelo `HttpContext` . Essas interfaces podem ser adicionadas, modificadas, encapsuladas, substituídas ou até mesmo removidas pelo servidor ou middleware, pois a solicitação é processada sem a necessidade de reimplementar todo o processo `HttpContext` . Eles também podem ser usados para simular a funcionalidade durante o teste.

## <a name="feature-collections"></a>Coleções de recursos

A <xref:Microsoft.AspNetCore.Http.HttpContext.Features> propriedade de `HttpContext` fornece acesso à coleção de interfaces de recurso para a solicitação atual. Como a coleção de recursos é mutável, mesmo no contexto de uma solicitação, o middleware pode ser usado para modificar a coleção e adicionar suporte para recursos adicionais. Alguns recursos avançados só estão disponíveis acessando a interface associada por meio da coleção de recursos.

## <a name="feature-interfaces"></a>Interfaces de recurso

ASP.NET Core define um número de interfaces de recurso HTTP comuns no <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> , que são compartilhadas por vários servidores e middleware para identificar os recursos aos quais dão suporte. Os servidores e o middleware também podem fornecer suas próprias interfaces com funcionalidade adicional.

A maioria das interfaces de recursos fornecerá funcionalidade opcional, de iluminação e suas `HttpContext` APIs associadas fornecerão padrões se o recurso não estiver presente. Algumas interfaces são indicadas no conteúdo a seguir, conforme necessário, pois elas fornecem a funcionalidade principal de solicitação e resposta e devem ser implementadas para processar a solicitação.

As seguintes interfaces de recurso são de <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> :

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Define a estrutura de uma solicitação HTTP, incluindo o protocolo, o caminho, a cadeia de caracteres de consulta, os cabeçalhos e o corpo. Esse recurso é necessário para processar solicitações.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Define a estrutura de uma resposta HTTP, incluindo o código de status, os cabeçalhos e o corpo da resposta. Esse recurso é necessário para processar solicitações.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Define diferentes maneiras de gravar o corpo da resposta, usando um `Stream` , um `PipeWriter` ou um arquivo. Esse recurso é necessário para processar solicitações. Isso substitui `IHttpResponseFeature.Body` e `IHttpSendFileFeature` .

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Mantém o <xref:System.Security.Claims.ClaimsPrincipal> associado à solicitação no momento.

<xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Usado para analisar e armazenar em cache os envios de formulários de entrada de várias partes e HTTP.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Usado para controlar se as operações de e/s síncronas são permitidas para os corpos de solicitação ou resposta.

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Define métodos para desabilitar o buffer de solicitações e/ou respostas.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Define propriedades para a ID de conexão e endereços locais e remotos e portas.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controla o tamanho máximo de corpo de solicitação permitido para a solicitação atual.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

`IHttpRequestBodyDetectionFeature`: Indica se a solicitação pode ter um corpo.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Adiciona uma propriedade que pode ser implementada para identificar solicitações exclusivamente.

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Define o suporte para anular conexões ou detectar se uma solicitação foi encerrada prematuramente, como por uma desconexão do cliente.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Fornece acesso aos cabeçalhos de trailer de solicitação, se houver.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Usado para enviar mensagens de redefinição para protocolos que dão suporte a eles como HTTP/2 ou HTTP/3.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Permite que o aplicativo forneça cabeçalhos de trailer de resposta, se houver suporte.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Define um método para enviar arquivos de forma assíncrona.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Define o suporte para [atualizações http](https://tools.ietf.org/html/rfc2616.html#section-14.42), que permitem ao cliente especificar quais protocolos adicionais ele gostaria de usar se o servidor quiser alternar protocolos.

<xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Define uma API para dar suporte a soquetes da Web.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controla se a compactação de resposta deve ser usada em conexões HTTPS.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Armazena a <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> coleção para o estado do aplicativo por solicitação.

<xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Analisa e armazena em cache a cadeia de caracteres de consulta.
   
::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Representa o corpo da solicitação como um <xref:System.IO.Pipelines.PipeReader> .
 
::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Analisa e armazena em cache os valores de cabeçalho da solicitação `Cookie` .

<xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Controla como cookie as respostas s são aplicadas ao `Set-Cookie` cabeçalho.

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: Esse recurso fornece acesso a variáveis de servidor de solicitação, como as fornecidas pelo IIS.

::: moniker-end
   
<xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Fornece acesso a um <xref:System.IServiceProvider> com serviços de solicitação com escopo.

<xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Define `ISessionFactory` e <xref:Microsoft.AspNetCore.Http.ISession> abstrações para dar suporte a sessões de usuário. `ISessionFeature` é implementado pelo <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (consulte <xref:fundamentals/app-state> ).

<xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Define uma API para recuperar certificados de cliente.

<xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Define métodos para trabalhar com parâmetros de associação de token TLS.
   
::: moniker range=">= aspnetcore-2.2"
   
<xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Usado para consultar, conceder e retirar o consentimento do usuário em relação ao armazenamento de informações do usuário relacionadas à atividade e à funcionalidade do site.
   
::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
