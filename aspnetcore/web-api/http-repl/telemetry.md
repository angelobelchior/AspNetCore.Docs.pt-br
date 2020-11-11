---
title: Telemetria HTTP REPL
author: scottaddie
description: Saiba mais sobre a telemetria coletada pelo REPL HTTP.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/10/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 8590959e43c2dda69090acb358e740b271426a44
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502014"
---
# <a name="http-repl-telemetry"></a><span data-ttu-id="f3a70-103">Telemetria HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="f3a70-103">HTTP REPL telemetry</span></span>

<span data-ttu-id="f3a70-104">O [loop Read-eval-Print (repl) http](xref:web-api/http-repl) inclui um recurso de telemetria que coleta dados de uso.</span><span class="sxs-lookup"><span data-stu-id="f3a70-104">The [HTTP Read-Eval-Print Loop (REPL)](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="f3a70-105">É importante que a equipe de REPL HTTP entenda como a ferramenta é usada para que possa ser melhorada.</span><span class="sxs-lookup"><span data-stu-id="f3a70-105">It's important that the HTTP REPL team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="f3a70-106">Como recusar</span><span class="sxs-lookup"><span data-stu-id="f3a70-106">How to opt out</span></span>

<span data-ttu-id="f3a70-107">O recurso de telemetria HTTP REPL é habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="f3a70-107">The HTTP REPL telemetry feature is enabled by default.</span></span> <span data-ttu-id="f3a70-108">Para recusar o recurso de telemetria, defina a variável de ambiente `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` como `1` ou `true`.</span><span class="sxs-lookup"><span data-stu-id="f3a70-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="f3a70-109">Divulgação</span><span class="sxs-lookup"><span data-stu-id="f3a70-109">Disclosure</span></span>

<span data-ttu-id="f3a70-110">HttpRepl exibe um texto semelhante ao seguinte quando você executa a ferramenta pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="f3a70-110">HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="f3a70-111">O texto pode variar um pouco dependendo da versão da ferramenta que você está executando.</span><span class="sxs-lookup"><span data-stu-id="f3a70-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="f3a70-112">Essa experiência de “primeira execução” é como a Microsoft notifica você sobre a coleta de dados.</span><span class="sxs-lookup"><span data-stu-id="f3a70-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

## <a name="data-points"></a><span data-ttu-id="f3a70-113">Pontos de dados</span><span class="sxs-lookup"><span data-stu-id="f3a70-113">Data points</span></span>

<span data-ttu-id="f3a70-114">O recurso telemetria não:</span><span class="sxs-lookup"><span data-stu-id="f3a70-114">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="f3a70-115">Coletar dados pessoais, como nomes de acessados, endereços de email ou URLs.</span><span class="sxs-lookup"><span data-stu-id="f3a70-115">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="f3a70-116">Verifique suas solicitações ou respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3a70-116">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="f3a70-117">Os dados são enviados com segurança aos servidores da Microsoft e mantidos sob acesso restrito.</span><span class="sxs-lookup"><span data-stu-id="f3a70-117">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="f3a70-118">A proteção de sua privacidade é importante para nós.</span><span class="sxs-lookup"><span data-stu-id="f3a70-118">Protecting your privacy is important to us.</span></span> <span data-ttu-id="f3a70-119">Se você suspeitar de que o recurso de telemetria está coletando dados confidenciais ou se os dados estiverem sendo inseguros ou indevidamente manipulados, execute uma das seguintes ações:</span><span class="sxs-lookup"><span data-stu-id="f3a70-119">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="f3a70-120">Arquivo de um problema no repositório [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) .</span><span class="sxs-lookup"><span data-stu-id="f3a70-120">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="f3a70-121">Envie um email para [dotnet@microsoft.com](mailto:dotnet@microsoft.com) para investigação.</span><span class="sxs-lookup"><span data-stu-id="f3a70-121">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="f3a70-122">O recurso telemetria coleta os dados a seguir.</span><span class="sxs-lookup"><span data-stu-id="f3a70-122">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="f3a70-123">Versões do SDK do .NET</span><span class="sxs-lookup"><span data-stu-id="f3a70-123">.NET SDK versions</span></span> | <span data-ttu-id="f3a70-124">Dados</span><span class="sxs-lookup"><span data-stu-id="f3a70-124">Data</span></span> |
|--------------|------|
| <span data-ttu-id="f3a70-125">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-125">>=5.0</span></span>        | <span data-ttu-id="f3a70-126">Carimbo de data/hora da invocação.</span><span class="sxs-lookup"><span data-stu-id="f3a70-126">Timestamp of invocation.</span></span> |
| <span data-ttu-id="f3a70-127">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-127">>=5.0</span></span>        | <span data-ttu-id="f3a70-128">Endereço IP de três octetos usado para determinar a localização geográfica.</span><span class="sxs-lookup"><span data-stu-id="f3a70-128">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="f3a70-129">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-129">>=5.0</span></span>        | <span data-ttu-id="f3a70-130">Sistema operacional e versão.</span><span class="sxs-lookup"><span data-stu-id="f3a70-130">Operating system and version.</span></span> |
| <span data-ttu-id="f3a70-131">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-131">>=5.0</span></span>        | <span data-ttu-id="f3a70-132">ID de tempo de execução (RID) em que a ferramenta está sendo executada.</span><span class="sxs-lookup"><span data-stu-id="f3a70-132">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="f3a70-133">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-133">>=5.0</span></span>        | <span data-ttu-id="f3a70-134">Se a ferramenta está sendo executada em um contêiner.</span><span class="sxs-lookup"><span data-stu-id="f3a70-134">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="f3a70-135">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-135">>=5.0</span></span>        | <span data-ttu-id="f3a70-136">Endereço MAC (controle de acesso à mídia) com hash: um ID exclusivo e com hash criptográfico (SHA256) para um computador.</span><span class="sxs-lookup"><span data-stu-id="f3a70-136">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="f3a70-137">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-137">>=5.0</span></span>        | <span data-ttu-id="f3a70-138">Versão do kernel.</span><span class="sxs-lookup"><span data-stu-id="f3a70-138">Kernel version.</span></span> |
| <span data-ttu-id="f3a70-139">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-139">>=5.0</span></span>        | <span data-ttu-id="f3a70-140">Versão de REPL HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3a70-140">HTTP REPL version.</span></span> |
| <span data-ttu-id="f3a70-141">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-141">>=5.0</span></span>        | <span data-ttu-id="f3a70-142">Se a ferramenta foi iniciada `help` com `run` argumentos,, ou `connect` .</span><span class="sxs-lookup"><span data-stu-id="f3a70-142">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="f3a70-143">Os valores de argumento reais não são coletados.</span><span class="sxs-lookup"><span data-stu-id="f3a70-143">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="f3a70-144">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-144">>=5.0</span></span>        | <span data-ttu-id="f3a70-145">Comando invocado (por exemplo, `get` ) e se foi bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="f3a70-145">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="f3a70-146">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-146">>=5.0</span></span>        | <span data-ttu-id="f3a70-147">Para o `connect` comando, se os `root` `base` argumentos, ou `openapi` foram fornecidos.</span><span class="sxs-lookup"><span data-stu-id="f3a70-147">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="f3a70-148">Os valores de argumento reais não são coletados.</span><span class="sxs-lookup"><span data-stu-id="f3a70-148">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="f3a70-149">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-149">>=5.0</span></span>        | <span data-ttu-id="f3a70-150">Para o `pref` comando, se um `get` ou `set` foi emitido e qual preferência foi acessada.</span><span class="sxs-lookup"><span data-stu-id="f3a70-150">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="f3a70-151">Se não for uma preferência conhecida, o nome será com hash.</span><span class="sxs-lookup"><span data-stu-id="f3a70-151">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="f3a70-152">O valor não é coletado.</span><span class="sxs-lookup"><span data-stu-id="f3a70-152">The value isn't collected.</span></span> |
| <span data-ttu-id="f3a70-153">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-153">>=5.0</span></span>        | <span data-ttu-id="f3a70-154">Para o `set header` comando, o nome do cabeçalho que está sendo definido.</span><span class="sxs-lookup"><span data-stu-id="f3a70-154">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="f3a70-155">Se não for um cabeçalho bem conhecido, o nome será com hash.</span><span class="sxs-lookup"><span data-stu-id="f3a70-155">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="f3a70-156">O valor não é coletado.</span><span class="sxs-lookup"><span data-stu-id="f3a70-156">The value isn't collected.</span></span> |
| <span data-ttu-id="f3a70-157">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-157">>=5.0</span></span>        | <span data-ttu-id="f3a70-158">Para o `connect` comando, se um caso especial para `dotnet new webapi` foi usado e, se ele foi ignorado por meio de preferência.</span><span class="sxs-lookup"><span data-stu-id="f3a70-158">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="f3a70-159">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="f3a70-159">>=5.0</span></span>        | <span data-ttu-id="f3a70-160">Para todos os comandos HTTP (por exemplo, GET, POST, PUT), se cada uma das opções foi especificada.</span><span class="sxs-lookup"><span data-stu-id="f3a70-160">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="f3a70-161">Os valores das opções não são coletados.</span><span class="sxs-lookup"><span data-stu-id="f3a70-161">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="f3a70-162">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f3a70-162">Additional resources</span></span>

* [<span data-ttu-id="f3a70-163">Telemetria do SDK do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f3a70-163">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="f3a70-164">CLI do .NET Core dados de telemetria</span><span class="sxs-lookup"><span data-stu-id="f3a70-164">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
