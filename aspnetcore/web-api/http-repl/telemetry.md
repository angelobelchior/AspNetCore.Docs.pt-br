---
title: Telemetria HttpRepl
author: scottaddie
description: Saiba mais sobre a telemetria coletada pelo HttpRepl.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550602"
---
# <a name="httprepl-telemetry"></a><span data-ttu-id="0857a-103">Telemetria HttpRepl</span><span class="sxs-lookup"><span data-stu-id="0857a-103">HttpRepl telemetry</span></span>

<span data-ttu-id="0857a-104">O [HttpRepl](xref:web-api/http-repl) inclui um recurso de telemetria que coleta dados de uso.</span><span class="sxs-lookup"><span data-stu-id="0857a-104">The [HttpRepl](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="0857a-105">É importante que a equipe HttpRepl entenda como a ferramenta é usada para que possa ser melhorada.</span><span class="sxs-lookup"><span data-stu-id="0857a-105">It's important that the HttpRepl team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="0857a-106">Como recusar</span><span class="sxs-lookup"><span data-stu-id="0857a-106">How to opt out</span></span>

<span data-ttu-id="0857a-107">O recurso telemetria do HttpRepl é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="0857a-107">The HttpRepl telemetry feature is enabled by default.</span></span> <span data-ttu-id="0857a-108">Para recusar o recurso de telemetria, defina a variável de ambiente `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` como `1` ou `true`.</span><span class="sxs-lookup"><span data-stu-id="0857a-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="0857a-109">Divulgação</span><span class="sxs-lookup"><span data-stu-id="0857a-109">Disclosure</span></span>

<span data-ttu-id="0857a-110">O HttpRepl exibe um texto semelhante ao seguinte quando você executa a ferramenta pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="0857a-110">The HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="0857a-111">O texto pode variar um pouco dependendo da versão da ferramenta que você está executando.</span><span class="sxs-lookup"><span data-stu-id="0857a-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="0857a-112">Essa experiência de “primeira execução” é como a Microsoft notifica você sobre a coleta de dados.</span><span class="sxs-lookup"><span data-stu-id="0857a-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

<span data-ttu-id="0857a-113">Para suprimir o texto da experiência de "primeira execução", defina a `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` variável de ambiente como `1` ou `true` .</span><span class="sxs-lookup"><span data-stu-id="0857a-113">To suppress the "first run" experience text, set the `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` environment variable to `1` or `true`.</span></span>

## <a name="data-points"></a><span data-ttu-id="0857a-114">Pontos de dados</span><span class="sxs-lookup"><span data-stu-id="0857a-114">Data points</span></span>

<span data-ttu-id="0857a-115">O recurso telemetria não:</span><span class="sxs-lookup"><span data-stu-id="0857a-115">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="0857a-116">Coletar dados pessoais, como nomes de acessados, endereços de email ou URLs.</span><span class="sxs-lookup"><span data-stu-id="0857a-116">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="0857a-117">Verifique suas solicitações ou respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="0857a-117">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="0857a-118">Os dados são enviados com segurança aos servidores da Microsoft e mantidos sob acesso restrito.</span><span class="sxs-lookup"><span data-stu-id="0857a-118">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="0857a-119">A proteção de sua privacidade é importante para nós.</span><span class="sxs-lookup"><span data-stu-id="0857a-119">Protecting your privacy is important to us.</span></span> <span data-ttu-id="0857a-120">Se você suspeitar de que o recurso de telemetria está coletando dados confidenciais ou se os dados estiverem sendo inseguros ou indevidamente manipulados, execute uma das seguintes ações:</span><span class="sxs-lookup"><span data-stu-id="0857a-120">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="0857a-121">Arquivo de um problema no repositório [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) .</span><span class="sxs-lookup"><span data-stu-id="0857a-121">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="0857a-122">Envie um email para [dotnet@microsoft.com](mailto:dotnet@microsoft.com) para investigação.</span><span class="sxs-lookup"><span data-stu-id="0857a-122">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="0857a-123">O recurso telemetria coleta os dados a seguir.</span><span class="sxs-lookup"><span data-stu-id="0857a-123">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="0857a-124">Versões do SDK do .NET</span><span class="sxs-lookup"><span data-stu-id="0857a-124">.NET SDK versions</span></span> | <span data-ttu-id="0857a-125">Dados</span><span class="sxs-lookup"><span data-stu-id="0857a-125">Data</span></span> |
|--------------|------|
| <span data-ttu-id="0857a-126">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-126">>=5.0</span></span>        | <span data-ttu-id="0857a-127">Carimbo de data/hora da invocação.</span><span class="sxs-lookup"><span data-stu-id="0857a-127">Timestamp of invocation.</span></span> |
| <span data-ttu-id="0857a-128">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-128">>=5.0</span></span>        | <span data-ttu-id="0857a-129">Endereço IP de três octetos usado para determinar a localização geográfica.</span><span class="sxs-lookup"><span data-stu-id="0857a-129">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="0857a-130">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-130">>=5.0</span></span>        | <span data-ttu-id="0857a-131">Sistema operacional e versão.</span><span class="sxs-lookup"><span data-stu-id="0857a-131">Operating system and version.</span></span> |
| <span data-ttu-id="0857a-132">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-132">>=5.0</span></span>        | <span data-ttu-id="0857a-133">ID de tempo de execução (RID) em que a ferramenta está sendo executada.</span><span class="sxs-lookup"><span data-stu-id="0857a-133">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="0857a-134">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-134">>=5.0</span></span>        | <span data-ttu-id="0857a-135">Se a ferramenta está sendo executada em um contêiner.</span><span class="sxs-lookup"><span data-stu-id="0857a-135">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="0857a-136">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-136">>=5.0</span></span>        | <span data-ttu-id="0857a-137">Endereço MAC (controle de acesso à mídia) com hash: um ID exclusivo e com hash criptográfico (SHA256) para um computador.</span><span class="sxs-lookup"><span data-stu-id="0857a-137">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="0857a-138">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-138">>=5.0</span></span>        | <span data-ttu-id="0857a-139">Versão do kernel.</span><span class="sxs-lookup"><span data-stu-id="0857a-139">Kernel version.</span></span> |
| <span data-ttu-id="0857a-140">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-140">>=5.0</span></span>        | <span data-ttu-id="0857a-141">Versão do HttpRepl.</span><span class="sxs-lookup"><span data-stu-id="0857a-141">HttpRepl version.</span></span> |
| <span data-ttu-id="0857a-142">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-142">>=5.0</span></span>        | <span data-ttu-id="0857a-143">Se a ferramenta foi iniciada `help` com `run` argumentos,, ou `connect` .</span><span class="sxs-lookup"><span data-stu-id="0857a-143">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="0857a-144">Os valores de argumento reais não são coletados.</span><span class="sxs-lookup"><span data-stu-id="0857a-144">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="0857a-145">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-145">>=5.0</span></span>        | <span data-ttu-id="0857a-146">Comando invocado (por exemplo, `get` ) e se foi bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="0857a-146">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="0857a-147">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-147">>=5.0</span></span>        | <span data-ttu-id="0857a-148">Para o `connect` comando, se os `root` `base` argumentos, ou `openapi` foram fornecidos.</span><span class="sxs-lookup"><span data-stu-id="0857a-148">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="0857a-149">Os valores de argumento reais não são coletados.</span><span class="sxs-lookup"><span data-stu-id="0857a-149">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="0857a-150">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-150">>=5.0</span></span>        | <span data-ttu-id="0857a-151">Para o `pref` comando, se um `get` ou `set` foi emitido e qual preferência foi acessada.</span><span class="sxs-lookup"><span data-stu-id="0857a-151">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="0857a-152">Se não for uma preferência conhecida, o nome será com hash.</span><span class="sxs-lookup"><span data-stu-id="0857a-152">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="0857a-153">O valor não é coletado.</span><span class="sxs-lookup"><span data-stu-id="0857a-153">The value isn't collected.</span></span> |
| <span data-ttu-id="0857a-154">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-154">>=5.0</span></span>        | <span data-ttu-id="0857a-155">Para o `set header` comando, o nome do cabeçalho que está sendo definido.</span><span class="sxs-lookup"><span data-stu-id="0857a-155">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="0857a-156">Se não for um cabeçalho bem conhecido, o nome será com hash.</span><span class="sxs-lookup"><span data-stu-id="0857a-156">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="0857a-157">O valor não é coletado.</span><span class="sxs-lookup"><span data-stu-id="0857a-157">The value isn't collected.</span></span> |
| <span data-ttu-id="0857a-158">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-158">>=5.0</span></span>        | <span data-ttu-id="0857a-159">Para o `connect` comando, se um caso especial para `dotnet new webapi` foi usado e, se ele foi ignorado por meio de preferência.</span><span class="sxs-lookup"><span data-stu-id="0857a-159">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="0857a-160">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="0857a-160">>=5.0</span></span>        | <span data-ttu-id="0857a-161">Para todos os comandos HTTP (por exemplo, GET, POST, PUT), se cada uma das opções foi especificada.</span><span class="sxs-lookup"><span data-stu-id="0857a-161">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="0857a-162">Os valores das opções não são coletados.</span><span class="sxs-lookup"><span data-stu-id="0857a-162">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="0857a-163">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0857a-163">Additional resources</span></span>

* [<span data-ttu-id="0857a-164">Telemetria do SDK do .NET Core</span><span class="sxs-lookup"><span data-stu-id="0857a-164">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="0857a-165">CLI do .NET Core dados de telemetria</span><span class="sxs-lookup"><span data-stu-id="0857a-165">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
