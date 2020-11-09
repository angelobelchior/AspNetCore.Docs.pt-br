---
title: Impedir XSS (script entre sites) no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre scripts entre sites (XSS) e técnicas para abordar essa vulnerabilidade em um aplicativo ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
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
uid: security/cross-site-scripting
ms.openlocfilehash: 1c90a786efe8c3c205a729a2da9d3a99d0222012
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053079"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="c8410-103">Impedir XSS (script entre sites) no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8410-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="c8410-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c8410-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c8410-105">O XSS (script entre sites) é uma vulnerabilidade de segurança que permite que um invasor coloque scripts do lado do cliente (geralmente JavaScript) em páginas da Web.</span><span class="sxs-lookup"><span data-stu-id="c8410-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="c8410-106">Quando outros usuários carregam páginas afetadas, os scripts do invasor serão executados, permitindo que o invasor roube cookie s e tokens de sessão, altere o conteúdo da página da Web por meio da manipulação DOM ou redirecione o navegador para outra página.</span><span class="sxs-lookup"><span data-stu-id="c8410-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="c8410-107">As vulnerabilidades de XSS geralmente ocorrem quando um aplicativo usa a entrada do usuário e a envia para uma página sem validar, codificar ou escapar dela.</span><span class="sxs-lookup"><span data-stu-id="c8410-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="c8410-108">Protegendo seu aplicativo contra XSS</span><span class="sxs-lookup"><span data-stu-id="c8410-108">Protecting your application against XSS</span></span>

<span data-ttu-id="c8410-109">Em um nível básico, o XSS funciona ao enganar seu aplicativo para inserir uma `<script>` marca na página renderizada ou ao inserir um `On*` evento em um elemento.</span><span class="sxs-lookup"><span data-stu-id="c8410-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="c8410-110">Os desenvolvedores devem usar as seguintes etapas de prevenção para evitar a introdução de XSS em seus aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c8410-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="c8410-111">Nunca coloque dados não confiáveis em sua entrada HTML, a menos que você siga o restante das etapas abaixo.</span><span class="sxs-lookup"><span data-stu-id="c8410-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="c8410-112">Os dados não confiáveis são todos os dados que podem ser controlados por um invasor, entradas de formulário HTML, cadeias de caracteres de consulta, cabeçalhos HTTP, até mesmo dados provenientes de um banco de dado como um invasor pode violar seu banco mesmo se não puderem violar seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c8410-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="c8410-113">Antes de colocar dados não confiáveis dentro de um elemento HTML, verifique se ele é codificado em HTML.</span><span class="sxs-lookup"><span data-stu-id="c8410-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="c8410-114">A codificação HTML leva caracteres como &lt; e os altera em uma forma segura, como &amp; lt;</span><span class="sxs-lookup"><span data-stu-id="c8410-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="c8410-115">Antes de colocar dados não confiáveis em um atributo HTML, verifique se ele é codificado em HTML.</span><span class="sxs-lookup"><span data-stu-id="c8410-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="c8410-116">A codificação de atributo HTML é um superconjunto de codificação HTML e codifica caracteres adicionais como "e".</span><span class="sxs-lookup"><span data-stu-id="c8410-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="c8410-117">Antes de colocar dados não confiáveis no JavaScript, coloque os dados em um elemento HTML cujo conteúdo você recupera no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="c8410-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="c8410-118">Se isso não for possível, verifique se os dados são codificados em JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c8410-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="c8410-119">A codificação JavaScript leva caracteres perigosos para o JavaScript e os substitui por seu Hex, por exemplo, &lt; seria codificada como `\u003C` .</span><span class="sxs-lookup"><span data-stu-id="c8410-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="c8410-120">Antes de colocar dados não confiáveis em uma cadeia de caracteres de consulta de URL, verifique se ela é codificada em URL.</span><span class="sxs-lookup"><span data-stu-id="c8410-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-no-locrazor"></a><span data-ttu-id="c8410-121">Codificação HTML usando Razor</span><span class="sxs-lookup"><span data-stu-id="c8410-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="c8410-122">O Razor mecanismo usado no MVC codifica automaticamente todas as saídas originadas de variáveis, a menos que você trabalhe muito difícil para impedi-lo de fazer isso.</span><span class="sxs-lookup"><span data-stu-id="c8410-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="c8410-123">Ele usa regras de codificação de atributo HTML sempre que você usa a *@* diretiva.</span><span class="sxs-lookup"><span data-stu-id="c8410-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="c8410-124">Como a codificação de atributo HTML é um superconjunto de codificação HTML isso significa que você não precisa se preocupar com a possibilidade de usar codificação HTML ou codificação de atributo HTML.</span><span class="sxs-lookup"><span data-stu-id="c8410-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="c8410-125">Você deve garantir que use apenas @ em um contexto HTML, não ao tentar inserir uma entrada não confiável diretamente no JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c8410-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="c8410-126">Os auxiliares de marca também codificam a entrada usada nos parâmetros de marca.</span><span class="sxs-lookup"><span data-stu-id="c8410-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="c8410-127">Faça o seguinte Razor modo de exibição:</span><span class="sxs-lookup"><span data-stu-id="c8410-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="c8410-128">Essa exibição gera o conteúdo da variável *untrustedInput* .</span><span class="sxs-lookup"><span data-stu-id="c8410-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="c8410-129">Essa variável inclui alguns caracteres que são usados em ataques XSS, &lt; ou seja, "e &gt; .</span><span class="sxs-lookup"><span data-stu-id="c8410-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="c8410-130">Examinar a origem mostra a saída renderizada codificada como:</span><span class="sxs-lookup"><span data-stu-id="c8410-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="c8410-131">ASP.NET Core MVC fornece uma `HtmlString` classe que não é codificada automaticamente na saída.</span><span class="sxs-lookup"><span data-stu-id="c8410-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="c8410-132">Isso nunca deve ser usado em combinação com a entrada não confiável, pois isso vai expor uma vulnerabilidade de XSS.</span><span class="sxs-lookup"><span data-stu-id="c8410-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-no-locrazor"></a><span data-ttu-id="c8410-133">Codificação JavaScript usando Razor</span><span class="sxs-lookup"><span data-stu-id="c8410-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="c8410-134">Pode haver ocasiões em que você deseja inserir um valor em JavaScript para processar em sua exibição.</span><span class="sxs-lookup"><span data-stu-id="c8410-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="c8410-135">Há duas maneiras de fazer isso.</span><span class="sxs-lookup"><span data-stu-id="c8410-135">There are two ways to do this.</span></span> <span data-ttu-id="c8410-136">A maneira mais segura de inserir valores é inserir o valor em um atributo de dados de uma marca e recuperá-lo em seu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c8410-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="c8410-137">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="c8410-137">For example:</span></span>

```cshtml
@{
    var untrustedInput = "<script>alert(1)</script>";
}

<div id="injectedData"
     data-untrustedinput="@untrustedInput" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it
    // can lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

<span data-ttu-id="c8410-138">A marcação anterior gera o seguinte HTML:</span><span class="sxs-lookup"><span data-stu-id="c8410-138">The preceding markup generates the following HTML:</span></span>

```html
<div id="injectedData"
     data-untrustedinput="&lt;script&gt;alert(1)&lt;/script&gt;" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it can
    // lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

<span data-ttu-id="c8410-139">O código anterior gera a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="c8410-139">The preceding code generates the following output:</span></span>

```
<script>alert(1)</script>
<script>alert(1)</script>
<script>alert(1)</script>
```

>[!WARNING]
> <span data-ttu-id="c8410-140">\* **Não** _ concatenar entrada não confiável em JavaScript para criar elementos DOM ou usar `document.write()` em conteúdo gerado dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="c8410-140">Do \* **NOT** _ concatenate untrusted input in JavaScript to create DOM elements or use `document.write()` on dynamically generated content.</span></span>
>
> <span data-ttu-id="c8410-141">Use uma das abordagens a seguir para impedir que o código seja exposto a XSS baseado em DOM: _ `createElement()` e atribua valores de propriedade com métodos ou Propriedades apropriadas, como o `node.textContent=` ou o nó. InnerText = '.</span><span class="sxs-lookup"><span data-stu-id="c8410-141">Use one of the following approaches to prevent code from being exposed to DOM-based XSS: _ `createElement()` and assign property values with appropriate methods or properties such as `node.textContent=` or node.InnerText=\`.</span></span>
> * <span data-ttu-id="c8410-142">`document.CreateTextNode()` e acrescentá-lo no local do DOM apropriado.</span><span class="sxs-lookup"><span data-stu-id="c8410-142">`document.CreateTextNode()` and append it in the appropriate DOM location.</span></span>
> * `element.SetAttribute()`
> * `element[attribute]=`

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="c8410-143">Acessando codificadores no código</span><span class="sxs-lookup"><span data-stu-id="c8410-143">Accessing encoders in code</span></span>

<span data-ttu-id="c8410-144">Os codificadores HTML, JavaScript e URL estão disponíveis para seu código de duas maneiras, você pode injeta-los por meio de [injeção de dependência](xref:fundamentals/dependency-injection) ou pode usar os codificadores padrão contidos no `System.Text.Encodings.Web` namespace.</span><span class="sxs-lookup"><span data-stu-id="c8410-144">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="c8410-145">Se você usar os codificadores padrão, qualquer um que você aplicou aos intervalos de caracteres a serem tratados como seguros não entrará em vigor – os codificadores padrão usam as regras de codificação mais seguras possíveis.</span><span class="sxs-lookup"><span data-stu-id="c8410-145">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="c8410-146">Para usar os codificadores configuráveis por meio de DI, seus construtores devem pegar um parâmetro de *HtmlEncode* , *JavaScriptEncoder* e *urlencoder* conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="c8410-146">To use the configurable encoders via DI your constructors should take an *HtmlEncoder* , *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="c8410-147">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="c8410-147">For example;</span></span>

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a><span data-ttu-id="c8410-148">Parâmetros de URL de codificação</span><span class="sxs-lookup"><span data-stu-id="c8410-148">Encoding URL Parameters</span></span>

<span data-ttu-id="c8410-149">Se você quiser criar uma cadeia de caracteres de consulta de URL com entrada não confiável como um valor, use o `UrlEncoder` para codificar o valor.</span><span class="sxs-lookup"><span data-stu-id="c8410-149">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="c8410-150">Por exemplo,</span><span class="sxs-lookup"><span data-stu-id="c8410-150">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="c8410-151">Após a codificação, a variável EncodedValue conterá `%22Quoted%20Value%20with%20spaces%20and%20%26%22` .</span><span class="sxs-lookup"><span data-stu-id="c8410-151">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="c8410-152">Espaços, aspas, pontuação e outros caracteres não seguros serão codificados por porcentagem para seu valor hexadecimal, por exemplo, um caractere de espaço se tornará %20.</span><span class="sxs-lookup"><span data-stu-id="c8410-152">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="c8410-153">Não use a entrada não confiável como parte de um caminho de URL.</span><span class="sxs-lookup"><span data-stu-id="c8410-153">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="c8410-154">Sempre passe uma entrada não confiável como um valor de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="c8410-154">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="c8410-155">Personalizando os codificadores</span><span class="sxs-lookup"><span data-stu-id="c8410-155">Customizing the Encoders</span></span>

<span data-ttu-id="c8410-156">Por padrão, os codificadores usam uma lista segura limitada ao intervalo Unicode latino básico e codificam todos os caracteres fora desse intervalo como seus equivalentes de código de caractere.</span><span class="sxs-lookup"><span data-stu-id="c8410-156">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="c8410-157">Esse comportamento também afeta a Razor renderização TagHelper e HtmlHelper, pois ela usará os codificadores para gerar suas cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="c8410-157">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="c8410-158">O raciocínio por trás disso é proteger contra bugs de navegador desconhecidos ou futuros (os bugs anteriores do navegador acabaram analisando com base no processamento de caracteres que não são do inglês).</span><span class="sxs-lookup"><span data-stu-id="c8410-158">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="c8410-159">Se o seu site faz uso intensivo de caracteres não latinos, como chinês, cirílico ou outros, isso provavelmente não é o comportamento desejado.</span><span class="sxs-lookup"><span data-stu-id="c8410-159">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="c8410-160">Você pode personalizar as listas de codificador com segurança para incluir intervalos Unicode apropriados ao seu aplicativo durante a inicialização, no `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="c8410-160">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="c8410-161">Por exemplo, usando a configuração padrão, você pode usar um Razor HtmlHelper como esse;</span><span class="sxs-lookup"><span data-stu-id="c8410-161">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="c8410-162">Quando você exibir a origem da página da Web, verá que ela foi renderizada da seguinte maneira, com o texto codificado em Chinês;</span><span class="sxs-lookup"><span data-stu-id="c8410-162">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="c8410-163">Para ampliar os caracteres tratados como seguros pelo codificador, você inseriria a seguinte linha no `ConfigureServices()` método em `startup.cs` ;</span><span class="sxs-lookup"><span data-stu-id="c8410-163">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="c8410-164">Este exemplo amplia a lista segura para incluir o intervalo Unicode CjkUnifiedIdeographs.</span><span class="sxs-lookup"><span data-stu-id="c8410-164">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="c8410-165">A saída renderizada agora se tornaria</span><span class="sxs-lookup"><span data-stu-id="c8410-165">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="c8410-166">Os intervalos de lista segura são especificados como gráficos de código Unicode, não idiomas.</span><span class="sxs-lookup"><span data-stu-id="c8410-166">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="c8410-167">O [padrão Unicode](https://unicode.org/) tem uma lista de [gráficos de código](https://www.unicode.org/charts/index.html) que você pode usar para localizar o gráfico que contém seus caracteres.</span><span class="sxs-lookup"><span data-stu-id="c8410-167">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="c8410-168">Cada codificador, HTML, JavaScript e URL deve ser configurado separadamente.</span><span class="sxs-lookup"><span data-stu-id="c8410-168">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="c8410-169">A personalização da lista segura afeta apenas os codificadores originados por meio de DI.</span><span class="sxs-lookup"><span data-stu-id="c8410-169">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="c8410-170">Se você acessar diretamente um codificador por meio `System.Text.Encodings.Web.*Encoder.Default` do padrão, somente a SafeList básica será usada.</span><span class="sxs-lookup"><span data-stu-id="c8410-170">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="c8410-171">Onde a codificação deve ocorrer?</span><span class="sxs-lookup"><span data-stu-id="c8410-171">Where should encoding take place?</span></span>

<span data-ttu-id="c8410-172">A prática aceita geral é que a codificação ocorre no ponto de saída e os valores codificados nunca devem ser armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c8410-172">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="c8410-173">A codificação no ponto de saída permite que você altere o uso de dados, por exemplo, de HTML para um valor de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="c8410-173">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="c8410-174">Ele também permite que você pesquise seus dados facilmente sem precisar codificar valores antes de Pesquisar e permite aproveitar as alterações ou correções de bugs feitas aos codificadores.</span><span class="sxs-lookup"><span data-stu-id="c8410-174">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="c8410-175">Validação como uma técnica de prevenção de XSS</span><span class="sxs-lookup"><span data-stu-id="c8410-175">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="c8410-176">A validação pode ser uma ferramenta útil para limitar ataques de XSS.</span><span class="sxs-lookup"><span data-stu-id="c8410-176">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="c8410-177">Por exemplo, uma cadeia de caracteres numérica contendo apenas os caracteres 0-9 não disparará um ataque XSS.</span><span class="sxs-lookup"><span data-stu-id="c8410-177">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="c8410-178">A validação se torna mais complicada ao aceitar HTML na entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="c8410-178">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="c8410-179">A análise da entrada HTML é difícil, se não impossível.</span><span class="sxs-lookup"><span data-stu-id="c8410-179">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="c8410-180">A redução, acoplada a um analisador que retira o HTML inserido, é uma opção mais segura para aceitar a entrada avançada.</span><span class="sxs-lookup"><span data-stu-id="c8410-180">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="c8410-181">Nunca confie apenas na validação.</span><span class="sxs-lookup"><span data-stu-id="c8410-181">Never rely on validation alone.</span></span> <span data-ttu-id="c8410-182">Sempre codifique a entrada não confiável antes da saída, não importa qual validação ou limpeza foi executada.</span><span class="sxs-lookup"><span data-stu-id="c8410-182">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
