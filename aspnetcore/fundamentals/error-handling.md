---
title: Tratar erros no ASP.NET Core
author: rick-anderson
description: Descubra como tratar erros em aplicativos ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: da7f50b27e447b86bd8a06851b767488d51b7050
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592885"
---
# <a name="handle-errors-in-aspnet-core"></a>Tratar erros no ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

Por [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/)e [Steve Smith](https://ardalis.com/)

Este artigo aborda abordagens comuns para lidar com erros em ASP.NET Core aplicativos Web. Consulte <xref:web-api/handle-errors> para APIs da Web.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Como baixar](xref:index#how-to-download-a-sample).) A guia rede nas ferramentas de desenvolvedor de navegador F12 é útil ao testar o aplicativo de exemplo.

## <a name="developer-exception-page"></a>Página de exceção do desenvolvedor

A *Página de exceção do desenvolvedor* exibe informações detalhadas sobre as exceções de solicitação. Os modelos de ASP.NET Core geram o seguinte código:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

O código realçado anterior habilita a página de exceção do desenvolvedor quando o aplicativo está em execução no [ambiente de desenvolvimento](xref:fundamentals/environments).

Os modelos colocam no <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> início do pipeline de middleware para que ele possa detectar exceções lançadas no middleware a seguir.

O código anterior habilita a página de exceção do desenvolvedor ***somente*** quando o aplicativo é executado no ambiente de desenvolvimento. As informações detalhadas de exceção não devem ser exibidas publicamente quando o aplicativo é executado no ambiente de produção. Para saber mais sobre a configuração de ambientes, confira <xref:fundamentals/environments>.

A página de exceção do desenvolvedor inclui as seguintes informações sobre a exceção e a solicitação:

* Rastreamento de pilha
* Parâmetros de cadeia de caracteres de consulta, se houver
* Cookies se houver
* Cabeçalhos

## <a name="exception-handler-page"></a>Página do Manipulador de exceção

Para configurar uma página de tratamento de erros personalizada para o [ambiente de produção](xref:fundamentals/environments), chame <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> . Este middleware de manipulação de exceção:

* Captura e registra em log as exceções.
* Executa novamente a solicitação em um pipeline alternativo usando o caminho indicado. A solicitação não será executada novamente se a resposta tiver sido iniciada. O código gerado pelo modelo executa novamente a solicitação usando o `/Error` caminho.

No exemplo a seguir, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adiciona o middleware de manipulação de exceção em ambientes que não são de desenvolvimento:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

O Razor modelo de aplicativo pages fornece uma página de erro (*. cshtml*) e uma <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) na pasta *páginas* . Para um aplicativo MVC, o modelo de projeto inclui um `Error` método de ação e uma exibição de erro para o controlador inicial.

Não marque o método de ação do manipulador de erros com atributos do método HTTP, como `HttpGet` . Os verbos explícitos impedem que algumas solicitações atinjam o método de ação. Permitir acesso anônimo ao método se usuários não autenticados devem ver o modo de exibição de erro.

### <a name="access-the-exception"></a>Acessar a exceção

Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acessar a exceção e o caminho de solicitação original em um manipulador de erro. O código a seguir adiciona `ExceptionMessage` as *páginas padrão/Error. cshtml. cs* geradas pelos modelos de ASP.NET Core:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> **Não** forneça informações de erro confidenciais aos clientes. Fornecer erros é um risco à segurança.

Para testar a exceção no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Defina o ambiente como produção.
* Remova os comentários de `webBuilder.UseStartup<Startup>();` em *Program.cs*.
* Selecione **disparar uma exceção** no Home Page.

## <a name="exception-handler-lambda"></a>Lambda do Manipulador de exceção

Uma alternativa a uma [página personalizada de manipulador de exceção](#exception-handler-page) é fornecer um lambda a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>. Usar um lambda permite acessar o erro antes de retornar a resposta.

O código a seguir usa um lambda para manipulação de exceção:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> **Não** forneça informações de erro confidenciais de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para clientes. Fornecer erros é um risco à segurança.

Para testar o lambda de manipulação de exceção no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Defina o ambiente como produção.
* Remova os comentários de `webBuilder.UseStartup<StartupLambda>();` em *Program.cs*.
* Selecione **disparar uma exceção** no Home Page.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Por padrão, um aplicativo ASP.NET Core não fornece uma página de código de status para códigos de status de erro HTTP, como *404-não encontrado*. Quando o aplicativo encontra uma condição de erro HTTP 400-499 que não tem um corpo, ele retorna o código de status e um corpo de resposta vazio. Para fornecer páginas de código de status, use o middleware de páginas de código de status. Para habilitar os manipuladores padrão somente texto para os códigos de status de erros comuns, chame <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> no método `Startup.Configure`:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<!-- Review: 
When you comment out // UseExceptionHandler("/Error");`
you get a browser dependant error, not the codepage as I expected.
call /index/2 -> return StatusCode(500); -> you get the codepage 
-->

Chame `UseStatusCodePages` antes do middleware de tratamento de solicitação. Por exemplo, chame `UseStatusCodePages` antes do middleware de arquivo estático e do middleware de pontos de extremidade.

Quando `UseStatusCodePages` não é usado, navegar para uma URL sem um ponto de extremidade retorna uma mensagem de erro dependente do navegador indicando que o ponto de extremidade não foi encontrado. Por exemplo, navegando até `Home/Privacy2` . Quando `UseStatusCodePages` é chamado, o navegador retorna:

```html
Status Code: 404; Not Found
```

`UseStatusCodePages` Normalmente, não é usado em produção porque retorna uma mensagem que não é útil para os usuários.

Para testar `UseStatusCodePages` no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Defina o ambiente como produção.
* Remova os comentários de `webBuilder.UseStartup<StartupUseStatusCodePages>();` em *Program.cs*.
* Selecione os links no home page na home page.

### <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages com cadeia de caracteres de formato

Para personalizar o texto e o tipo de conteúdo de resposta, use uma sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que leva um tipo de conteúdo e uma cadeia de caracteres de formato:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

No código anterior, `{0}` é um espaço reservado para o código de erro.

`UseStatusCodePages` com uma cadeia de caracteres de formato geralmente não é usada na produção porque ela retorna uma mensagem que não é útil para os usuários.

Para testar `UseStatusCodePages` no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remova os comentários de `webBuilder.UseStartup<StartupFormat>();` em *Program.cs*.

### <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages com lambda

Para especificar a manipulação de erro personalizada e o código de gravação de resposta, use a sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que leva uma expressão lambda:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

`UseStatusCodePages` com um lambda normalmente não é usado em produção porque retorna uma mensagem que não é útil para os usuários.

Para testar `UseStatusCodePages` no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remova os comentários de `webBuilder.UseStartup<StartupStatusLambda>();` em *Program.cs*.

### <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

O método de extensão <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:

* Envia um código de status [302 – Encontrado](https://developer.mozilla.org/docs/Web/HTTP/Status/302) ao cliente.
* Redireciona o cliente para o ponto de extremidade de tratamento de erros fornecido no modelo de URL. O ponto de extremidade de tratamento de erros normalmente exibe informações de erro e retorna HTTP 200.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

O modelo de URL pode incluir um `{0}` espaço reservado para o código de status, conforme mostrado no código anterior. Se o modelo de URL começar com `~` (til), o `~` será substituído pelo aplicativo `PathBase` . Ao especificar um ponto de extremidade no aplicativo, crie uma exibição ou Razor página do MVC para o ponto de extremidade. Para obter um Razor exemplo de páginas, consulte [páginas/mystatuscode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Este método normalmente é usado quando o aplicativo:

* Deveria redirecionar o cliente para um terminal diferente, geralmente em situações nas quais um aplicativo diferente processa o erro. Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade redirecionado.
* Não deveria preservar e retornar o código de status original com a resposta de redirecionamento inicial.

Para testar `UseStatusCodePages` no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remova os comentários de `webBuilder.UseStartup<StartupSCredirect>();` em *Program.cs*.

### <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

O método de extensão <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:

* Retorna o código de status original ao cliente.
* Gera o corpo da resposta ao executar novamente o pipeline de solicitação por meio de um caminho alternativo.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

Se um ponto de extremidade dentro do aplicativo for especificado, crie uma exibição ou Razor página do MVC para o ponto de extremidade. Certifique-se `UseStatusCodePagesWithReExecute` de que é colocado antes `UseRouting` para que a solicitação possa ser redirecionada para a página de status. Para obter um Razor exemplo de páginas, consulte [pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Este método normalmente é usado quando o aplicativo tem que:

* Processar a solicitação sem redirecionar para um ponto de extremidade diferente. Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade originalmente solicitado.
* Preservar e retornar o código de status original com a resposta.

A URL e os modelos de cadeia de caracteres de consulta podem incluir um espaço reservado `{0}` para o código de status. O modelo de URL deve começar com `/` .

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

O ponto de extremidade que processa o erro pode obter a URL original que gerou o erro, conforme mostrado no exemplo a seguir:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

Para obter um Razor exemplo de páginas, consulte [pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Para testar `UseStatusCodePages` no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remova os comentários de `webBuilder.UseStartup<StartupSCreX>();` em *Program.cs*.

## <a name="disable-status-code-pages"></a>Desabilitar páginas de código de status

Para desabilitar as páginas de código de status de um método de ação ou controlador MVC, use o atributo [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute).

Para desabilitar as páginas de código de status para solicitações específicas em um Razor método de manipulador de páginas ou em um controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a>Código de tratamento de exceção

O código nas páginas de manipulação de exceção também pode gerar exceções. As páginas de erro de produção devem ser testadas minuciosamente e tomar cuidado adicional para evitar gerar exceções próprias.
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a>Cabeçalhos de resposta

Depois que os cabeçalhos de resposta são enviados:

* O aplicativo já não consegue alterar o código de status da resposta.
* As páginas de exceção ou manipuladores não podem ser executados. A resposta deve ser concluída ou a conexão será anulada.

## <a name="server-exception-handling"></a>Tratamento de exceções do servidor

Além da lógica de tratamento de exceção em um aplicativo, a [implementação do servidor http](xref:fundamentals/servers/index) pode lidar com algumas exceções. Se o servidor capturar uma exceção antes de os cabeçalhos de resposta serem enviados, o servidor enviará uma `500 - Internal Server Error` resposta sem um corpo de resposta. Se o servidor capturar uma exceção depois que os cabeçalhos de resposta forem enviados, o servidor fechará a conexão. As solicitações que não são tratadas pelo aplicativo são manipuladas pelo servidor. Qualquer exceção que ocorrer quando o servidor estiver tratando a solicitação será tratada pelo tratamento de exceção do servidor. As páginas de erro personalizadas do aplicativo, o middleware de tratamento de exceção e os filtros configurados não afetam esse comportamento.

## <a name="startup-exception-handling"></a>Tratamento de exceção na inicialização

Apenas a camada de hospedagem pode tratar exceções que ocorrem durante a inicialização do aplicativo. O host pode ser configurado para [capturar erros de inicialização](xref:fundamentals/host/web-host#capture-startup-errors) e [capturar erros detalhados](xref:fundamentals/host/web-host#detailed-errors).

A camada de hospedagem só poderá mostrar uma página de erro para um erro de inicialização capturado se o erro ocorrer após a associação de endereço do host/porta. Se a associação falhar:

* A camada de hospedagem registrará uma exceção crítica.
* O processo dotnet falhará.
* Nenhuma página de erro é exibida quando o servidor HTTP é [Kestrel](xref:fundamentals/servers/kestrel).

Quando executado no [IIS](/iis) (ou no Serviço de Aplicativo do Azure) ou no [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), um erro *502.5 Falha no Processo* será retornado pelo [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se o processo não puder ser iniciado. Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Página de erro do banco de dados

O filtro de exceção da página do desenvolvedor de banco de dados `AddDatabaseDeveloperPageExceptionFilter` captura as exceções relacionadas ao banco de dados que podem ser resolvidas usando Entity Framework Core migrações. Quando essas exceções ocorrem, uma resposta HTML é gerada com detalhes de possíveis ações para resolver o problema. Esta página é habilitada apenas no ambiente de desenvolvimento. O código a seguir foi gerado pelo ASP.NET Core Razor modelos de páginas quando contas de usuário individuais foram especificadas:

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a>Filtros de exceção

Em aplicativos MVC, os filtros de exceção podem ser configurados globalmente, por controlador ou por ação. Em Razor páginas aplicativos, eles podem ser configurados globalmente ou por modelo de página. Esses filtros manipulam quaisquer exceções sem tratamento que ocorrem durante a execução de uma ação do controlador ou outro filtro. Para obter mais informações, consulte <xref:mvc/controllers/filters#exception-filters>.

Filtros de exceção são úteis para interceptar exceções que ocorrem em ações do MVC, mas não são tão flexíveis quanto o middleware de [manipulação de exceção](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs)interno, `UseExceptionHandler` . É recomendável usar `UseExceptionHandler` , a menos que você precise executar o tratamento de erros de forma diferente com base em qual ação MVC é escolhida.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a>Erros de estado do modelo

Confira informações sobre como lidar com erros de estado de modelo em [model binding](xref:mvc/models/model-binding) e [Validação de modelos](xref:mvc/models/validation).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Por  [Tom Dykstra](https://github.com/tdykstra/)e [Steve Smith](https://ardalis.com/)

Este artigo aborda abordagens comuns para lidar com erros em ASP.NET Core aplicativos Web. Consulte <xref:web-api/handle-errors> para APIs da Web.

[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Como baixar](xref:index#how-to-download-a-sample).)

## <a name="developer-exception-page"></a>Página de exceção do desenvolvedor

A *Página de exceção do desenvolvedor* exibe informações detalhadas sobre as exceções de solicitação. Os modelos de ASP.NET Core geram o seguinte código:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

O código anterior habilita a página de exceção do desenvolvedor quando o aplicativo está em execução no [ambiente de desenvolvimento](xref:fundamentals/environments).

Os modelos <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> são colocados antes de qualquer middleware, portanto, as exceções são capturadas no middleware a seguir.

O código anterior habilita a página de exceção do desenvolvedor **somente quando o aplicativo está em execução no ambiente de desenvolvimento**. As informações detalhadas de exceção não devem ser exibidas publicamente quando o aplicativo é executado na produção. Para saber mais sobre a configuração de ambientes, confira <xref:fundamentals/environments>.

A página de exceção do desenvolvedor inclui as seguintes informações sobre a exceção e a solicitação:

* Rastreamento de pilha
* Parâmetros de cadeia de caracteres de consulta, se houver
* Cookies se houver
* Cabeçalhos

## <a name="exception-handler-page"></a>Página do Manipulador de exceção

Use o Middleware de tratamento de exceção para configurar uma página personalizada de tratamento de erro para o ambiente de produção. O middleware:

* Captura e registra em log as exceções.
* Executa novamente a solicitação em um pipeline alternativo para a página ou controlador indicado. A solicitação não será executada novamente se a resposta tiver sido iniciada. O código gerado pelo modelo executa novamente a solicitação para `/Error` .

No exemplo a seguir, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> inclui o Middleware de Manipulação de Exceções em ambientes que não são de desenvolvimento:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

O Razor modelo de aplicativo pages fornece uma página de erro (*. cshtml*) e uma <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> classe ( `ErrorModel` ) na pasta *páginas* . Para um aplicativo MVC, o modelo de projeto inclui um método de ação de erro e uma exibição de erro no controlador inicial.

Não marque o método de ação do manipulador de erros com atributos do método HTTP, como `HttpGet` . Verbos explícitos impedem algumas solicitações de chegar ao método. Permitir acesso anônimo ao método se usuários não autenticados devem ver o modo de exibição de erro.

### <a name="access-the-exception"></a>Acessar a exceção

Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para acessar a exceção e o caminho de solicitação original em uma página ou controlador de manipulador de erro:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> **Não** forneça informações de erro confidenciais aos clientes. Fornecer erros é um risco à segurança.

Para disparar a página de tratamento de exceção anterior, defina o ambiente como produções e Force uma exceção.

## <a name="exception-handler-lambda"></a>Lambda do Manipulador de exceção

Uma alternativa a uma [página personalizada de manipulador de exceção](#exception-handler-page) é fornecer um lambda a <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>. Usar um lambda permite acessar o erro antes de retornar a resposta.

Este é um exemplo de como usar um lambda para a manipulação de exceção:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

No código anterior, `await context.Response.WriteAsync(new string(' ', 512));` é adicionado para que o navegador Internet Explorer exiba a mensagem de erro em vez de uma mensagem de erro do IE. Para obter mais informações, consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).

> [!WARNING]
> **Não** forneça informações de erro confidenciais de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> para clientes. Fornecer erros é um risco à segurança.

Para ver o resultado do lambda de tratamento de exceções no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use as diretivas de pré-processador `ProdEnvironment` e `ErrorHandlerLambda` e selecione **Disparar uma exceção** na página inicial.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Por padrão, o aplicativo ASP.NET Core não fornece uma página de código de status para códigos de status HTTP, como *404 - Não Encontrado*. O aplicativo retornar um código de status e um corpo de resposta vazio. Use o middleware das Páginas de código de status para fornecer páginas de código de status.

O middleware é disponibilizado pelo pacote [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) .

Para habilitar os manipuladores padrão somente texto para os códigos de status de erros comuns, chame <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> no método `Startup.Configure`:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Chame `UseStatusCodePages` antes do middleware de tratamento da solicitação (por exemplo, o middleware de arquivos estáticos e o middleware MVC).

Quando `UseStatusCodePages` não é usado, navegar para uma URL sem um ponto de extremidade retorna uma mensagem de erro dependente do navegador indicando que o ponto de extremidade não foi encontrado. Por exemplo, navegando até `Home/Privacy2` . Quando `UseStatusCodePages` é chamado, o navegador retorna:

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages com cadeia de caracteres de formato

Para personalizar o texto e o tipo de conteúdo de resposta, use uma sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que leva um tipo de conteúdo e uma cadeia de caracteres de formato:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages com lambda

Para especificar a manipulação de erro personalizada e o código de gravação de resposta, use a sobrecarga de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> que leva uma expressão lambda:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

O método de extensão <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:

* Envia um código de status *302 – Encontrado* ao cliente.
* Redireciona o cliente para o local fornecido no modelo de URL.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

O modelo de URL pode incluir um espaço reservado `{0}` para o código de status, conforme mostrado no exemplo. Se o modelo de URL começar com `~` (til), o `~` será substituído pelo aplicativo `PathBase` . Se você apontar para um ponto de extremidade dentro do aplicativo, crie uma exibição ou Razor página do MVC para o ponto de extremidade. Para obter um Razor exemplo de páginas, consulte *páginas/StatusCode. cshtml* no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Este método normalmente é usado quando o aplicativo:

* Deveria redirecionar o cliente para um terminal diferente, geralmente em situações nas quais um aplicativo diferente processa o erro. Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade redirecionado.
* Não deveria preservar e retornar o código de status original com a resposta de redirecionamento inicial.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

O método de extensão <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:

* Retorna o código de status original ao cliente.
* Gera o corpo da resposta ao executar novamente o pipeline de solicitação por meio de um caminho alternativo.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Se você apontar para um ponto de extremidade dentro do aplicativo, crie uma exibição ou Razor página do MVC para o ponto de extremidade. Certifique-se `UseStatusCodePagesWithReExecute` de que é colocado antes `UseRouting` para que a solicitação possa ser redirecionada para a página de status. Para obter um Razor exemplo de páginas, consulte *páginas/StatusCode. cshtml* no [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Este método normalmente é usado quando o aplicativo tem que:

* Processar a solicitação sem redirecionar para um ponto de extremidade diferente. Para aplicativos Web, a barra de endereços do navegador do cliente reflete o ponto de extremidade originalmente solicitado.
* Preservar e retornar o código de status original com a resposta.

Os modelos de URL e cadeia de caracteres de consulta podem incluir um espaço reservado (`{0}`) para o código de status. O modelo de URL deve começar com uma barra (`/`). Ao usar um espaço reservado no caminho, verifique se o ponto de extremidade (página ou controlador) pode processar o segmento de linha. Por exemplo, uma Razor página de erros deve aceitar o valor do segmento de caminho opcional com a `@page` diretiva:

```cshtml
@page "{code?}"
```

O ponto de extremidade que processa o erro pode obter a URL original que gerou o erro, conforme mostrado no exemplo a seguir:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Desabilitar páginas de código de status

Para desabilitar páginas de código de status para um controlador MVC ou um método de ação, use o [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) atributo.

Para desabilitar as páginas de código de status para solicitações específicas em um Razor método de manipulador de páginas ou em um controlador MVC, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Código de tratamento de exceção

Código em páginas de tratamento de exceção pode gerar exceções. Geralmente, é uma boa ideia que páginas de erro de produção sejam compostas por conteúdo puramente estático.

### <a name="response-headers"></a>Cabeçalhos de resposta

Depois que os cabeçalhos de resposta são enviados:

* O aplicativo já não consegue alterar o código de status da resposta.
* As páginas de exceção ou manipuladores não podem ser executados. A resposta deve ser concluída ou a conexão será anulada.

## <a name="server-exception-handling"></a>Tratamento de exceções do servidor

Além da lógica de tratamento de exceção no aplicativo, a [implementação do servidor HTTP](xref:fundamentals/servers/index) pode lidar com algumas exceções. Se o servidor capturar uma exceção antes que os cabeçalhos de resposta sejam enviados, o servidor enviará uma resposta *500 Erro Interno do Servidor* sem um corpo de resposta. Se o servidor capturar uma exceção depois que os cabeçalhos de resposta forem enviados, o servidor fechará a conexão. As solicitações que não são manipuladas pelo aplicativo são manipuladas pelo servidor. Qualquer exceção que ocorrer quando o servidor estiver tratando a solicitação será tratada pelo tratamento de exceção do servidor. As páginas de erro personalizadas do aplicativo, o middleware de tratamento de exceção e os filtros configurados não afetam esse comportamento.

## <a name="startup-exception-handling"></a>Tratamento de exceção na inicialização

Apenas a camada de hospedagem pode tratar exceções que ocorrem durante a inicialização do aplicativo. O host pode ser configurado para [capturar erros de inicialização](xref:fundamentals/host/web-host#capture-startup-errors) e [capturar erros detalhados](xref:fundamentals/host/web-host#detailed-errors).

A camada de hospedagem só poderá mostrar uma página de erro para um erro de inicialização capturado se o erro ocorrer após a associação de endereço do host/porta. Se a associação falhar:

* A camada de hospedagem registrará uma exceção crítica.
* O processo dotnet falhará.
* Nenhuma página de erro é exibida quando o servidor HTTP é [Kestrel](xref:fundamentals/servers/kestrel).

Quando executado no [IIS](/iis) (ou no Serviço de Aplicativo do Azure) ou no [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), um erro *502.5 Falha no Processo* será retornado pelo [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) se o processo não puder ser iniciado. Para obter mais informações, consulte <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Página de erro do banco de dados

O middleware da página de erro do banco de dados captura as exceções relacionadas ao banco de dados que podem ser resolvidas usando Entity Framework migrações. Quando estas exceções ocorrem, é gerada uma resposta HTML com detalhes das ações possíveis para resolver o problema. Esta página só deve ser habilitada no Ambiente de desenvolvimento. Habilite a página adicionando código a `Startup.Configure`:

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requer o pacote NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>Filtros de exceção

Em aplicativos MVC, os filtros de exceção podem ser configurados globalmente, por controlador ou por ação. Em Razor páginas aplicativos, eles podem ser configurados globalmente ou por modelo de página. Esses filtros tratam qualquer exceção sem tratamento ocorrida durante a execução de uma ação do controlador ou de outro filtro. Para obter mais informações, consulte <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Os filtros de exceção são úteis para interceptar exceções que ocorrem em ações do MVC, mas não são tão flexíveis quanto o middleware de tratamento de exceção. É recomendável usar o middleware. Use filtros somente onde você precisar fazer o tratamento de erro de forma diferente com base na ação de MVC escolhida.

## <a name="model-state-errors"></a>Erros de estado do modelo

Confira informações sobre como lidar com erros de estado de modelo em [model binding](xref:mvc/models/model-binding) e [Validação de modelos](xref:mvc/models/validation).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
