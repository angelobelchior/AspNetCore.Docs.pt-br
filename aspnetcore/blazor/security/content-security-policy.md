---
title: 'Impor uma política de segurança de conteúdo para ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: 'Saiba como usar uma política de segurança de conteúdo (CSP) com :::no-loc(Blazor)::: aplicativos ASP.NET Core para ajudar a proteger contra ataques XSS (scripts entre sites).'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/content-security-policy
ms.openlocfilehash: 744449240fabc3dae317d0d7bc9090311521c224
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570114"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-no-locblazor"></a><span data-ttu-id="17fae-103">Impor uma política de segurança de conteúdo para ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="17fae-103">Enforce a Content Security Policy for ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="17fae-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="17fae-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="17fae-105">O [XSS (script entre sites)](xref:security/cross-site-scripting) é uma vulnerabilidade de segurança em que um invasor coloca um ou mais scripts mal-intencionados do lado do cliente no conteúdo renderizado de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17fae-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="17fae-106">Uma política de segurança de conteúdo (CSP) ajuda a proteger contra ataques de XSS, informando o navegador de válido:</span><span class="sxs-lookup"><span data-stu-id="17fae-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="17fae-107">Fontes de conteúdo carregado, incluindo scripts, folhas de estilos e imagens.</span><span class="sxs-lookup"><span data-stu-id="17fae-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="17fae-108">Ações tomadas por uma página, especificando destinos de URL permitidos de formulários.</span><span class="sxs-lookup"><span data-stu-id="17fae-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="17fae-109">Plug-ins que podem ser carregados.</span><span class="sxs-lookup"><span data-stu-id="17fae-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="17fae-110">Para aplicar um CSP a um aplicativo, o desenvolvedor especifica várias *diretivas* de segurança de conteúdo do CSP em um ou mais `Content-Security-Policy` cabeçalhos ou `<meta>` marcas.</span><span class="sxs-lookup"><span data-stu-id="17fae-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="17fae-111">As políticas são avaliadas pelo navegador enquanto uma página está sendo carregada.</span><span class="sxs-lookup"><span data-stu-id="17fae-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="17fae-112">O navegador inspeciona as fontes da página e determina se elas atendem aos requisitos das diretivas de segurança do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="17fae-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="17fae-113">Quando as diretivas de política não são atendidas para um recurso, o navegador não carrega o recurso.</span><span class="sxs-lookup"><span data-stu-id="17fae-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="17fae-114">Por exemplo, considere uma política que não permita scripts de terceiros.</span><span class="sxs-lookup"><span data-stu-id="17fae-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="17fae-115">Quando uma página contém uma `<script>` marca com uma origem de terceiros no `src` atributo, o navegador impede que o script seja carregado.</span><span class="sxs-lookup"><span data-stu-id="17fae-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="17fae-116">O CSP tem suporte na maioria dos navegadores de desktop e móvel modernos, incluindo Chrome, Edge, Firefox, Opera e Safari.</span><span class="sxs-lookup"><span data-stu-id="17fae-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="17fae-117">O CSP é recomendado para :::no-loc(Blazor)::: aplicativos.</span><span class="sxs-lookup"><span data-stu-id="17fae-117">CSP is recommended for :::no-loc(Blazor)::: apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="17fae-118">Diretivas de política</span><span class="sxs-lookup"><span data-stu-id="17fae-118">Policy directives</span></span>

<span data-ttu-id="17fae-119">No mínimo, especifique as seguintes diretivas e fontes para :::no-loc(Blazor)::: aplicativos.</span><span class="sxs-lookup"><span data-stu-id="17fae-119">Minimally, specify the following directives and sources for :::no-loc(Blazor)::: apps.</span></span> <span data-ttu-id="17fae-120">Adicione outras diretivas e fontes conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="17fae-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="17fae-121">As seguintes diretivas são usadas na seção [aplicar a política](#apply-the-policy) deste artigo, onde as políticas de segurança de exemplo para o :::no-loc(Blazor WebAssembly)::: e :::no-loc(Blazor Server)::: são fornecidas:</span><span class="sxs-lookup"><span data-stu-id="17fae-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server)::: are provided:</span></span>

* <span data-ttu-id="17fae-122">[base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): restringe as URLs para a marca de uma página `<base>` .</span><span class="sxs-lookup"><span data-stu-id="17fae-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="17fae-123">Especifique `self` para indicar que a origem do aplicativo, incluindo o esquema e o número da porta, é uma origem válida.</span><span class="sxs-lookup"><span data-stu-id="17fae-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="17fae-124">[bloquear-tudo-conteúdo misto](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): impede o carregamento de conteúdo http e HTTPS mistos.</span><span class="sxs-lookup"><span data-stu-id="17fae-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="17fae-125">[Default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): indica um fallback para diretivas de origem que não são explicitamente especificadas pela política.</span><span class="sxs-lookup"><span data-stu-id="17fae-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="17fae-126">Especifique `self` para indicar que a origem do aplicativo, incluindo o esquema e o número da porta, é uma origem válida.</span><span class="sxs-lookup"><span data-stu-id="17fae-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="17fae-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): indica fontes válidas para imagens.</span><span class="sxs-lookup"><span data-stu-id="17fae-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="17fae-128">Especifique `data:` para permitir o carregamento de imagens de `data:` URLs.</span><span class="sxs-lookup"><span data-stu-id="17fae-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="17fae-129">Especifique `https:` para permitir o carregamento de imagens de pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="17fae-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="17fae-130">[objeto-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): indica fontes válidas para `<object>` as `<embed>` marcas, e `<applet>` .</span><span class="sxs-lookup"><span data-stu-id="17fae-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="17fae-131">Especifique `none` para impedir todas as fontes de URL.</span><span class="sxs-lookup"><span data-stu-id="17fae-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="17fae-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): indica fontes válidas para scripts.</span><span class="sxs-lookup"><span data-stu-id="17fae-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="17fae-133">Especifique a `https://stackpath.bootstrapcdn.com/` origem do host para scripts de bootstrap.</span><span class="sxs-lookup"><span data-stu-id="17fae-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="17fae-134">Especifique `self` para indicar que a origem do aplicativo, incluindo o esquema e o número da porta, é uma origem válida.</span><span class="sxs-lookup"><span data-stu-id="17fae-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="17fae-135">Em um :::no-loc(Blazor WebAssembly)::: aplicativo:</span><span class="sxs-lookup"><span data-stu-id="17fae-135">In a :::no-loc(Blazor WebAssembly)::: app:</span></span>
    * <span data-ttu-id="17fae-136">Especifique os hashes para permitir que os scripts necessários sejam carregados.</span><span class="sxs-lookup"><span data-stu-id="17fae-136">Specify hashes to permit required scripts to load.</span></span>
    * <span data-ttu-id="17fae-137">Especifique `unsafe-eval` para usar os `eval()` métodos e para a criação de código a partir de cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="17fae-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="17fae-138">Em um :::no-loc(Blazor Server)::: aplicativo, especifique hashes para permitir que os scripts necessários sejam carregados.</span><span class="sxs-lookup"><span data-stu-id="17fae-138">In a :::no-loc(Blazor Server)::: app, specify hashes to permit required scripts to load.</span></span>
* <span data-ttu-id="17fae-139">[Style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): indica fontes válidas para folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="17fae-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="17fae-140">Especifique a `https://stackpath.bootstrapcdn.com/` origem do host para as folhas de estilo de inicialização.</span><span class="sxs-lookup"><span data-stu-id="17fae-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="17fae-141">Especifique `self` para indicar que a origem do aplicativo, incluindo o esquema e o número da porta, é uma origem válida.</span><span class="sxs-lookup"><span data-stu-id="17fae-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="17fae-142">Especifique `unsafe-inline` para permitir o uso de estilos embutidos.</span><span class="sxs-lookup"><span data-stu-id="17fae-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="17fae-143">A declaração embutida é necessária para a interface do usuário em :::no-loc(Blazor Server)::: aplicativos para reconectar o cliente e o servidor após a solicitação inicial.</span><span class="sxs-lookup"><span data-stu-id="17fae-143">The inline declaration is required for the UI in :::no-loc(Blazor Server)::: apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="17fae-144">Em uma versão futura, o estilo embutido pode ser removido para que `unsafe-inline` não seja mais necessário.</span><span class="sxs-lookup"><span data-stu-id="17fae-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="17fae-145">[atualização-inseguro – solicitações](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): indica que as URLs de conteúdo de fontes inseguras (http) devem ser adquiridas com segurança via HTTPS.</span><span class="sxs-lookup"><span data-stu-id="17fae-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="17fae-146">As diretivas anteriores têm suporte de todos os navegadores, exceto o Microsoft Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="17fae-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="17fae-147">Para obter hashes de SHA para scripts embutidos adicionais:</span><span class="sxs-lookup"><span data-stu-id="17fae-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="17fae-148">Aplique o CSP mostrado na seção [aplicar a política](#apply-the-policy) .</span><span class="sxs-lookup"><span data-stu-id="17fae-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="17fae-149">Acesse o console de ferramentas de desenvolvedor do navegador ao executar o aplicativo localmente.</span><span class="sxs-lookup"><span data-stu-id="17fae-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="17fae-150">O navegador calcula e exibe hashes de scripts bloqueados quando um cabeçalho ou `meta` marca CSP está presente.</span><span class="sxs-lookup"><span data-stu-id="17fae-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="17fae-151">Copie os hashes fornecidos pelo navegador para as `script-src` fontes.</span><span class="sxs-lookup"><span data-stu-id="17fae-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="17fae-152">Use aspas simples em volta de cada hash.</span><span class="sxs-lookup"><span data-stu-id="17fae-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="17fae-153">Para uma matriz de suporte de navegador de nível 2 de política de segurança de conteúdo, consulte posso [usar: nível de política de segurança de conteúdo 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span><span class="sxs-lookup"><span data-stu-id="17fae-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="17fae-154">Aplicar a política</span><span class="sxs-lookup"><span data-stu-id="17fae-154">Apply the policy</span></span>

<span data-ttu-id="17fae-155">Use uma `<meta>` marca para aplicar a política:</span><span class="sxs-lookup"><span data-stu-id="17fae-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="17fae-156">Defina o valor do `http-equiv` atributo como `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="17fae-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="17fae-157">Coloque as diretivas no `content` valor do atributo.</span><span class="sxs-lookup"><span data-stu-id="17fae-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="17fae-158">Diretivas separadas com um ponto e vírgula ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="17fae-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="17fae-159">Sempre coloque a `meta` marca no `<head>` conteúdo.</span><span class="sxs-lookup"><span data-stu-id="17fae-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="17fae-160">As seções a seguir mostram as políticas de exemplo para o :::no-loc(Blazor WebAssembly)::: e o :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="17fae-160">The following sections show example policies for :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server):::.</span></span> <span data-ttu-id="17fae-161">Esses exemplos têm a versão deste artigo para cada versão do :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="17fae-161">These examples are versioned with this article for each release of :::no-loc(Blazor):::.</span></span> <span data-ttu-id="17fae-162">Para usar uma versão apropriada para sua versão, selecione a versão do documento com o seletor de **versão** na página da Web.</span><span class="sxs-lookup"><span data-stu-id="17fae-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="17fae-163">Na `<head>` página conteúdo do `wwwroot/index.html` host, aplique as diretivas descritas na seção [diretivas de política](#policy-directives) :</span><span class="sxs-lookup"><span data-stu-id="17fae-163">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

::: moniker range=">= aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

<span data-ttu-id="17fae-164">Adicione `script-src` `style-src` hashes adicionais, conforme exigido pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17fae-164">Add additional `script-src` and `style-src` hashes as required by the app.</span></span> <span data-ttu-id="17fae-165">Durante o desenvolvimento, use uma ferramenta online ou ferramentas de desenvolvedor de navegador para que os hashes sejam calculados para você.</span><span class="sxs-lookup"><span data-stu-id="17fae-165">During development, use an online tool or browser developer tools to have the hashes calculated for you.</span></span> <span data-ttu-id="17fae-166">Por exemplo, o erro do console de ferramentas do navegador a seguir relata o hash para um script necessário não coberto pela política:</span><span class="sxs-lookup"><span data-stu-id="17fae-166">For example, the following browser tools console error reports the hash for a required script not covered by the policy:</span></span>

> <span data-ttu-id="17fae-167">Você se recusou a executar o script embutido porque ele viola a seguinte diretiva de política de segurança de conteúdo: "... ".</span><span class="sxs-lookup"><span data-stu-id="17fae-167">Refused to execute inline script because it violates the following Content Security Policy directive: " ... ".</span></span> <span data-ttu-id="17fae-168">A palavra-chave ' unsafe-inline ', um hash (' SHA256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA = ') ou um nonce (' nonce-... ') é necessário para habilitar a execução embutida.</span><span class="sxs-lookup"><span data-stu-id="17fae-168">Either the 'unsafe-inline' keyword, a hash ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA='), or a nonce ('nonce-...') is required to enable inline execution.</span></span>

<span data-ttu-id="17fae-169">O script específico associado ao erro é exibido no console do próximo ao erro.</span><span class="sxs-lookup"><span data-stu-id="17fae-169">The particular script associated with the error is displayed in the console next to the error.</span></span>

### :::no-loc(Blazor Server):::

<span data-ttu-id="17fae-170">Na `<head>` página conteúdo do `Pages/_Host.cshtml` host, aplique as diretivas descritas na seção [diretivas de política](#policy-directives) :</span><span class="sxs-lookup"><span data-stu-id="17fae-170">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

::: moniker range=">= aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

<span data-ttu-id="17fae-171">Adicione `script-src` `style-src` hashes adicionais, conforme exigido pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="17fae-171">Add additional `script-src` and `style-src` hashes as required by the app.</span></span> <span data-ttu-id="17fae-172">Durante o desenvolvimento, use uma ferramenta online ou ferramentas de desenvolvedor de navegador para que os hashes sejam calculados para você.</span><span class="sxs-lookup"><span data-stu-id="17fae-172">During development, use an online tool or browser developer tools to have the hashes calculated for you.</span></span> <span data-ttu-id="17fae-173">Por exemplo, o erro do console de ferramentas do navegador a seguir relata o hash para um script necessário não coberto pela política:</span><span class="sxs-lookup"><span data-stu-id="17fae-173">For example, the following browser tools console error reports the hash for a required script not covered by the policy:</span></span>

> <span data-ttu-id="17fae-174">Você se recusou a executar o script embutido porque ele viola a seguinte diretiva de política de segurança de conteúdo: "... ".</span><span class="sxs-lookup"><span data-stu-id="17fae-174">Refused to execute inline script because it violates the following Content Security Policy directive: " ... ".</span></span> <span data-ttu-id="17fae-175">A palavra-chave ' unsafe-inline ', um hash (' SHA256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA = ') ou um nonce (' nonce-... ') é necessário para habilitar a execução embutida.</span><span class="sxs-lookup"><span data-stu-id="17fae-175">Either the 'unsafe-inline' keyword, a hash ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA='), or a nonce ('nonce-...') is required to enable inline execution.</span></span>

<span data-ttu-id="17fae-176">O script específico associado ao erro é exibido no console do próximo ao erro.</span><span class="sxs-lookup"><span data-stu-id="17fae-176">The particular script associated with the error is displayed in the console next to the error.</span></span>

## <a name="meta-tag-limitations"></a><span data-ttu-id="17fae-177">Limitações da marca meta</span><span class="sxs-lookup"><span data-stu-id="17fae-177">Meta tag limitations</span></span>

<span data-ttu-id="17fae-178">Uma `<meta>` política de marca não dá suporte às seguintes diretivas:</span><span class="sxs-lookup"><span data-stu-id="17fae-178">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="17fae-179">quadro-ancestrais</span><span class="sxs-lookup"><span data-stu-id="17fae-179">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="17fae-180">relatório-para</span><span class="sxs-lookup"><span data-stu-id="17fae-180">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="17fae-181">URI do relatório</span><span class="sxs-lookup"><span data-stu-id="17fae-181">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="17fae-182">AppDomain</span><span class="sxs-lookup"><span data-stu-id="17fae-182">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="17fae-183">Para dar suporte às diretivas anteriores, use um cabeçalho chamado `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="17fae-183">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="17fae-184">A cadeia de caracteres de diretiva é o valor do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="17fae-184">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="17fae-185">Testar uma política e receber relatórios de violação</span><span class="sxs-lookup"><span data-stu-id="17fae-185">Test a policy and receive violation reports</span></span>

<span data-ttu-id="17fae-186">O teste ajuda a confirmar se os scripts de terceiros não são bloqueados inadvertidamente durante a criação de uma política inicial.</span><span class="sxs-lookup"><span data-stu-id="17fae-186">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="17fae-187">Para testar uma política durante um período de tempo sem impor as diretivas de política, defina o `<meta>` atributo da marca `http-equiv` ou o nome do cabeçalho de uma política baseada em cabeçalho como `Content-Security-Policy-Report-Only` .</span><span class="sxs-lookup"><span data-stu-id="17fae-187">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="17fae-188">Os relatórios de falha são enviados como documentos JSON para uma URL especificada.</span><span class="sxs-lookup"><span data-stu-id="17fae-188">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="17fae-189">Para obter mais informações, consulte [MDN Web docs: content-Security-Policy-Report-only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="17fae-189">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="17fae-190">Para relatórios sobre violações enquanto uma política estiver ativa, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="17fae-190">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="17fae-191">relatório-para</span><span class="sxs-lookup"><span data-stu-id="17fae-191">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="17fae-192">URI do relatório</span><span class="sxs-lookup"><span data-stu-id="17fae-192">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="17fae-193">Embora `report-uri` o não seja mais recomendado para uso, ambas as diretivas devem ser usadas até que `report-to` o tenha suporte de todos os principais navegadores.</span><span class="sxs-lookup"><span data-stu-id="17fae-193">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="17fae-194">Não use exclusivamente `report-uri` porque o suporte para `report-uri` está sujeito a ser descartado a *qualquer momento* dos navegadores.</span><span class="sxs-lookup"><span data-stu-id="17fae-194">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="17fae-195">Remova o suporte para `report-uri` em suas políticas quando o `report-to` tiver suporte total.</span><span class="sxs-lookup"><span data-stu-id="17fae-195">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="17fae-196">Para acompanhar a adoção do `report-to` , consulte posso [usar: relatar](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span><span class="sxs-lookup"><span data-stu-id="17fae-196">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="17fae-197">Testar e atualizar a política de um aplicativo a cada versão.</span><span class="sxs-lookup"><span data-stu-id="17fae-197">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="17fae-198">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="17fae-198">Troubleshoot</span></span>

* <span data-ttu-id="17fae-199">Os erros aparecem no console de ferramentas para desenvolvedores do navegador.</span><span class="sxs-lookup"><span data-stu-id="17fae-199">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="17fae-200">Os navegadores fornecem informações sobre:</span><span class="sxs-lookup"><span data-stu-id="17fae-200">Browsers provide information about:</span></span>
  * <span data-ttu-id="17fae-201">Elementos que não estão em conformidade com a política.</span><span class="sxs-lookup"><span data-stu-id="17fae-201">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="17fae-202">Como modificar a política para permitir um item bloqueado.</span><span class="sxs-lookup"><span data-stu-id="17fae-202">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="17fae-203">Uma política só será completamente efetiva quando o navegador do cliente oferecer suporte a todas as diretivas incluídas.</span><span class="sxs-lookup"><span data-stu-id="17fae-203">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="17fae-204">Para obter uma matriz de suporte do navegador atual, consulte [posso usar: content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span><span class="sxs-lookup"><span data-stu-id="17fae-204">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17fae-205">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="17fae-205">Additional resources</span></span>

* [<span data-ttu-id="17fae-206">MDN Web docs: content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="17fae-206">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="17fae-207">Nível de política de segurança de conteúdo 2</span><span class="sxs-lookup"><span data-stu-id="17fae-207">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="17fae-208">Avaliador do Google CSP</span><span class="sxs-lookup"><span data-stu-id="17fae-208">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
