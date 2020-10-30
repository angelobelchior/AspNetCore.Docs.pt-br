---
title: Impedir XSS (script entre sites) no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre scripts entre sites (XSS) e técnicas para abordar essa vulnerabilidade em um aplicativo ASP.NET Core.
ms.author: riande
ms.date: 10/02/2018
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
uid: security/cross-site-scripting
ms.openlocfilehash: 1c90a786efe8c3c205a729a2da9d3a99d0222012
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053079"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>Impedir XSS (script entre sites) no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

O XSS (script entre sites) é uma vulnerabilidade de segurança que permite que um invasor coloque scripts do lado do cliente (geralmente JavaScript) em páginas da Web. Quando outros usuários carregam páginas afetadas, os scripts do invasor serão executados, permitindo que o invasor roube cookie s e tokens de sessão, altere o conteúdo da página da Web por meio da manipulação DOM ou redirecione o navegador para outra página. As vulnerabilidades de XSS geralmente ocorrem quando um aplicativo usa a entrada do usuário e a envia para uma página sem validar, codificar ou escapar dela.

## <a name="protecting-your-application-against-xss"></a>Protegendo seu aplicativo contra XSS

Em um nível básico, o XSS funciona ao enganar seu aplicativo para inserir uma `<script>` marca na página renderizada ou ao inserir um `On*` evento em um elemento. Os desenvolvedores devem usar as seguintes etapas de prevenção para evitar a introdução de XSS em seus aplicativos.

1. Nunca coloque dados não confiáveis em sua entrada HTML, a menos que você siga o restante das etapas abaixo. Os dados não confiáveis são todos os dados que podem ser controlados por um invasor, entradas de formulário HTML, cadeias de caracteres de consulta, cabeçalhos HTTP, até mesmo dados provenientes de um banco de dado como um invasor pode violar seu banco mesmo se não puderem violar seu aplicativo.

2. Antes de colocar dados não confiáveis dentro de um elemento HTML, verifique se ele é codificado em HTML. A codificação HTML leva caracteres como &lt; e os altera em uma forma segura, como &amp; lt;

3. Antes de colocar dados não confiáveis em um atributo HTML, verifique se ele é codificado em HTML. A codificação de atributo HTML é um superconjunto de codificação HTML e codifica caracteres adicionais como "e".

4. Antes de colocar dados não confiáveis no JavaScript, coloque os dados em um elemento HTML cujo conteúdo você recupera no tempo de execução. Se isso não for possível, verifique se os dados são codificados em JavaScript. A codificação JavaScript leva caracteres perigosos para o JavaScript e os substitui por seu Hex, por exemplo, &lt; seria codificada como `\u003C` .

5. Antes de colocar dados não confiáveis em uma cadeia de caracteres de consulta de URL, verifique se ela é codificada em URL.

## <a name="html-encoding-using-no-locrazor"></a>Codificação HTML usando Razor

O Razor mecanismo usado no MVC codifica automaticamente todas as saídas originadas de variáveis, a menos que você trabalhe muito difícil para impedi-lo de fazer isso. Ele usa regras de codificação de atributo HTML sempre que você usa a *@* diretiva. Como a codificação de atributo HTML é um superconjunto de codificação HTML isso significa que você não precisa se preocupar com a possibilidade de usar codificação HTML ou codificação de atributo HTML. Você deve garantir que use apenas @ em um contexto HTML, não ao tentar inserir uma entrada não confiável diretamente no JavaScript. Os auxiliares de marca também codificam a entrada usada nos parâmetros de marca.

Faça o seguinte Razor modo de exibição:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

Essa exibição gera o conteúdo da variável *untrustedInput* . Essa variável inclui alguns caracteres que são usados em ataques XSS, &lt; ou seja, "e &gt; . Examinar a origem mostra a saída renderizada codificada como:

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC fornece uma `HtmlString` classe que não é codificada automaticamente na saída. Isso nunca deve ser usado em combinação com a entrada não confiável, pois isso vai expor uma vulnerabilidade de XSS.

## <a name="javascript-encoding-using-no-locrazor"></a>Codificação JavaScript usando Razor

Pode haver ocasiões em que você deseja inserir um valor em JavaScript para processar em sua exibição. Há duas maneiras de fazer isso. A maneira mais segura de inserir valores é inserir o valor em um atributo de dados de uma marca e recuperá-lo em seu JavaScript. Por exemplo:

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

A marcação anterior gera o seguinte HTML:

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

O código anterior gera a seguinte saída:

```
<script>alert(1)</script>
<script>alert(1)</script>
<script>alert(1)</script>
```

>[!WARNING]
> * **Não** _ concatenar entrada não confiável em JavaScript para criar elementos DOM ou usar `document.write()` em conteúdo gerado dinamicamente.
>
> Use uma das abordagens a seguir para impedir que o código seja exposto a XSS baseado em DOM: _ `createElement()` e atribua valores de propriedade com métodos ou Propriedades apropriadas, como o `node.textContent=` ou o nó. InnerText = '.
> * `document.CreateTextNode()` e acrescentá-lo no local do DOM apropriado.
> * `element.SetAttribute()`
> * `element[attribute]=`

## <a name="accessing-encoders-in-code"></a>Acessando codificadores no código

Os codificadores HTML, JavaScript e URL estão disponíveis para seu código de duas maneiras, você pode injeta-los por meio de [injeção de dependência](xref:fundamentals/dependency-injection) ou pode usar os codificadores padrão contidos no `System.Text.Encodings.Web` namespace. Se você usar os codificadores padrão, qualquer um que você aplicou aos intervalos de caracteres a serem tratados como seguros não entrará em vigor – os codificadores padrão usam as regras de codificação mais seguras possíveis.

Para usar os codificadores configuráveis por meio de DI, seus construtores devem pegar um parâmetro de *HtmlEncode* , *JavaScriptEncoder* e *urlencoder* conforme apropriado. Por exemplo:

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

## <a name="encoding-url-parameters"></a>Parâmetros de URL de codificação

Se você quiser criar uma cadeia de caracteres de consulta de URL com entrada não confiável como um valor, use o `UrlEncoder` para codificar o valor. Por exemplo,

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Após a codificação, a variável EncodedValue conterá `%22Quoted%20Value%20with%20spaces%20and%20%26%22` . Espaços, aspas, pontuação e outros caracteres não seguros serão codificados por porcentagem para seu valor hexadecimal, por exemplo, um caractere de espaço se tornará %20.

>[!WARNING]
> Não use a entrada não confiável como parte de um caminho de URL. Sempre passe uma entrada não confiável como um valor de cadeia de caracteres de consulta.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>Personalizando os codificadores

Por padrão, os codificadores usam uma lista segura limitada ao intervalo Unicode latino básico e codificam todos os caracteres fora desse intervalo como seus equivalentes de código de caractere. Esse comportamento também afeta a Razor renderização TagHelper e HtmlHelper, pois ela usará os codificadores para gerar suas cadeias de caracteres.

O raciocínio por trás disso é proteger contra bugs de navegador desconhecidos ou futuros (os bugs anteriores do navegador acabaram analisando com base no processamento de caracteres que não são do inglês). Se o seu site faz uso intensivo de caracteres não latinos, como chinês, cirílico ou outros, isso provavelmente não é o comportamento desejado.

Você pode personalizar as listas de codificador com segurança para incluir intervalos Unicode apropriados ao seu aplicativo durante a inicialização, no `ConfigureServices()` .

Por exemplo, usando a configuração padrão, você pode usar um Razor HtmlHelper como esse;

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Quando você exibir a origem da página da Web, verá que ela foi renderizada da seguinte maneira, com o texto codificado em Chinês;

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Para ampliar os caracteres tratados como seguros pelo codificador, você inseriria a seguinte linha no `ConfigureServices()` método em `startup.cs` ;

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

Este exemplo amplia a lista segura para incluir o intervalo Unicode CjkUnifiedIdeographs. A saída renderizada agora se tornaria

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Os intervalos de lista segura são especificados como gráficos de código Unicode, não idiomas. O [padrão Unicode](https://unicode.org/) tem uma lista de [gráficos de código](https://www.unicode.org/charts/index.html) que você pode usar para localizar o gráfico que contém seus caracteres. Cada codificador, HTML, JavaScript e URL deve ser configurado separadamente.

> [!NOTE]
> A personalização da lista segura afeta apenas os codificadores originados por meio de DI. Se você acessar diretamente um codificador por meio `System.Text.Encodings.Web.*Encoder.Default` do padrão, somente a SafeList básica será usada.

## <a name="where-should-encoding-take-place"></a>Onde a codificação deve ocorrer?

A prática aceita geral é que a codificação ocorre no ponto de saída e os valores codificados nunca devem ser armazenados em um banco de dados. A codificação no ponto de saída permite que você altere o uso de dados, por exemplo, de HTML para um valor de cadeia de caracteres de consulta. Ele também permite que você pesquise seus dados facilmente sem precisar codificar valores antes de Pesquisar e permite aproveitar as alterações ou correções de bugs feitas aos codificadores.

## <a name="validation-as-an-xss-prevention-technique"></a>Validação como uma técnica de prevenção de XSS

A validação pode ser uma ferramenta útil para limitar ataques de XSS. Por exemplo, uma cadeia de caracteres numérica contendo apenas os caracteres 0-9 não disparará um ataque XSS. A validação se torna mais complicada ao aceitar HTML na entrada do usuário. A análise da entrada HTML é difícil, se não impossível. A redução, acoplada a um analisador que retira o HTML inserido, é uma opção mais segura para aceitar a entrada avançada. Nunca confie apenas na validação. Sempre codifique a entrada não confiável antes da saída, não importa qual validação ou limpeza foi executada.
