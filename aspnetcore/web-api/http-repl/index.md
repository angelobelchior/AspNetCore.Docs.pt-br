---
title: Testar APIs da Web com o HttpRepl
author: scottaddie
description: Saiba como usar a ferramenta global do HttpRepl .NET Core para navegar e testar uma API da Web do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
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
uid: web-api/http-repl
ms.openlocfilehash: 8dd763d270a00c9a71913d68d6a039b2d98c3864
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570205"
---
# <a name="test-web-apis-with-the-httprepl"></a><span data-ttu-id="64d8b-103">Testar APIs da Web com o HttpRepl</span><span class="sxs-lookup"><span data-stu-id="64d8b-103">Test web APIs with the HttpRepl</span></span>

<span data-ttu-id="64d8b-104">Por [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="64d8b-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="64d8b-105">O HTTP REPL (Read-Eval-Print Loop) é:</span><span class="sxs-lookup"><span data-stu-id="64d8b-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="64d8b-106">Uma ferramenta de linha de comando leve e multiplataforma compatível com todos os recursos compatíveis com o .NET Core.</span><span class="sxs-lookup"><span data-stu-id="64d8b-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="64d8b-107">Usado para fazer solicitações HTTP a fim de testar as APIs Web do ASP.NET Core (e as APIs Web não pertencentes ao ASP.NET Core) e exibir os resultados.</span><span class="sxs-lookup"><span data-stu-id="64d8b-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="64d8b-108">Capaz de testar APIs Web hospedadas em qualquer ambiente, inclusive no localhost e no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="64d8b-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="64d8b-109">Os [verbos HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) a seguir são compatíveis:</span><span class="sxs-lookup"><span data-stu-id="64d8b-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="64d8b-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="64d8b-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="64d8b-111">GET</span><span class="sxs-lookup"><span data-stu-id="64d8b-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="64d8b-112">PRINCIPAL</span><span class="sxs-lookup"><span data-stu-id="64d8b-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="64d8b-113">Opções</span><span class="sxs-lookup"><span data-stu-id="64d8b-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="64d8b-114">DISTRIBUÍDO</span><span class="sxs-lookup"><span data-stu-id="64d8b-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="64d8b-115">POST</span><span class="sxs-lookup"><span data-stu-id="64d8b-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="64d8b-116">PUT</span><span class="sxs-lookup"><span data-stu-id="64d8b-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="64d8b-117">Para acompanhar, [exiba ou baixe a API Web de exemplo do ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="64d8b-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="64d8b-118">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="64d8b-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="64d8b-119">Instalação</span><span class="sxs-lookup"><span data-stu-id="64d8b-119">Installation</span></span>

<span data-ttu-id="64d8b-120">Para instalar o HttpRepl, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="64d8b-120">To install the HttpRepl, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="64d8b-121">Uma [ferramenta global do .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) é instalada pelo pacote do NuGet [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl).</span><span class="sxs-lookup"><span data-stu-id="64d8b-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="64d8b-122">Uso</span><span class="sxs-lookup"><span data-stu-id="64d8b-122">Usage</span></span>

<span data-ttu-id="64d8b-123">Após a instalação bem-sucedida da ferramenta, execute o seguinte comando para iniciar o HttpRepl:</span><span class="sxs-lookup"><span data-stu-id="64d8b-123">After successful installation of the tool, run the following command to start the HttpRepl:</span></span>

```console
httprepl
```

<span data-ttu-id="64d8b-124">Para exibir os comandos HttpRepl disponíveis, execute um dos seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="64d8b-124">To view the available HttpRepl commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="64d8b-125">É exibida a saída a seguir:</span><span class="sxs-lookup"><span data-stu-id="64d8b-125">The following output is displayed:</span></span>

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

<span data-ttu-id="64d8b-126">O HttpRepl oferece a conclusão do comando.</span><span class="sxs-lookup"><span data-stu-id="64d8b-126">The HttpRepl offers command completion.</span></span> <span data-ttu-id="64d8b-127">Pressionar a tecla <kbd>Tab</kbd> itera na lista de comandos que completam os caracteres ou o ponto de extremidade da API que você digitou.</span><span class="sxs-lookup"><span data-stu-id="64d8b-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="64d8b-128">As seções a seguir descrevem os comandos da CLI disponíveis.</span><span class="sxs-lookup"><span data-stu-id="64d8b-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="64d8b-129">Conectar-se à API Web</span><span class="sxs-lookup"><span data-stu-id="64d8b-129">Connect to the web API</span></span>

<span data-ttu-id="64d8b-130">Conecte-se à uma API Web executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="64d8b-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="64d8b-131">`<ROOT URI>` é o URI de base para a API Web.</span><span class="sxs-lookup"><span data-stu-id="64d8b-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="64d8b-132">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="64d8b-133">Como alternativa, execute o seguinte comando a qualquer momento enquanto o HttpRepl estiver em execução:</span><span class="sxs-lookup"><span data-stu-id="64d8b-133">Alternatively, run the following command at any time while the HttpRepl is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="64d8b-134">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-134">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a><span data-ttu-id="64d8b-135">Apontar manualmente para a descrição do OpenAPI para a API Web</span><span class="sxs-lookup"><span data-stu-id="64d8b-135">Manually point to the OpenAPI description for the web API</span></span>

<span data-ttu-id="64d8b-136">O comando Connect acima tentará localizar a descrição de OpenAPI automaticamente.</span><span class="sxs-lookup"><span data-stu-id="64d8b-136">The connect command above will attempt to find the OpenAPI description automatically.</span></span> <span data-ttu-id="64d8b-137">Se, por algum motivo, não for possível fazer isso, você poderá especificar o URI da descrição de OpenAPI para a API Web usando a `--openapi` opção:</span><span class="sxs-lookup"><span data-stu-id="64d8b-137">If for some reason it's unable to do so, you can specify the URI of the OpenAPI description for the web API by using the `--openapi` option:</span></span>

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

<span data-ttu-id="64d8b-138">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-138">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a><span data-ttu-id="64d8b-139">Habilitar saída detalhada para obter detalhes sobre a pesquisa, análise e validação de descrição de OpenAPI</span><span class="sxs-lookup"><span data-stu-id="64d8b-139">Enable verbose output for details on OpenAPI description searching, parsing, and validation</span></span>

<span data-ttu-id="64d8b-140">A especificação da `--verbose` opção com o `connect` comando produzirá mais detalhes quando a ferramenta procurar a descrição de openapi, analisá-la e validá-la.</span><span class="sxs-lookup"><span data-stu-id="64d8b-140">Specifying the `--verbose` option with the `connect` command will produce more details when the tool searches for the OpenAPI description, parses, and validates it.</span></span>

```console
connect <ROOT URI> --verbose
```

<span data-ttu-id="64d8b-141">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-141">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="64d8b-142">Navegar na API Web</span><span class="sxs-lookup"><span data-stu-id="64d8b-142">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="64d8b-143">Exibir os pontos de extremidade disponíveis</span><span class="sxs-lookup"><span data-stu-id="64d8b-143">View available endpoints</span></span>

<span data-ttu-id="64d8b-144">Para listar os diferentes pontos de extremidade (controladores) no caminho atual do endereço da API Web, execute os comandos `ls` ou `dir`:</span><span class="sxs-lookup"><span data-stu-id="64d8b-144">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhost:5001/> ls
```

<span data-ttu-id="64d8b-145">O seguinte formato de saída será exibido:</span><span class="sxs-lookup"><span data-stu-id="64d8b-145">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="64d8b-146">A saída anterior indica que há dois controladores disponíveis: `Fruits` e `People`.</span><span class="sxs-lookup"><span data-stu-id="64d8b-146">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="64d8b-147">Ambos os controladores são compatíveis com operações HTTP GET e POST sem parâmetro.</span><span class="sxs-lookup"><span data-stu-id="64d8b-147">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="64d8b-148">Navegar em um controlador específico revela mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="64d8b-148">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="64d8b-149">Por exemplo, a saída do seguinte comando mostra que o controlador `Fruits` também é compatível com as operações HTTP GET, PUT e DELETE.</span><span class="sxs-lookup"><span data-stu-id="64d8b-149">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="64d8b-150">Cada uma dessas operações espera um parâmetro `id` na rota:</span><span class="sxs-lookup"><span data-stu-id="64d8b-150">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

<span data-ttu-id="64d8b-151">Outra opção é executar o comando `ui` para abrir a página da interface do usuário do Swagger da API Web em um navegador.</span><span class="sxs-lookup"><span data-stu-id="64d8b-151">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="64d8b-152">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-152">For example:</span></span>

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="64d8b-153">Navegar até um ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="64d8b-153">Navigate to an endpoint</span></span>

<span data-ttu-id="64d8b-154">Para navegar até um ponto de extremidade diferente na API Web, execute o comando `cd`:</span><span class="sxs-lookup"><span data-stu-id="64d8b-154">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/> cd people
```

<span data-ttu-id="64d8b-155">O caminho após o comando `cd` não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="64d8b-155">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="64d8b-156">O seguinte formato de saída será exibido:</span><span class="sxs-lookup"><span data-stu-id="64d8b-156">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-httprepl"></a><span data-ttu-id="64d8b-157">Personalizar o HttpRepl</span><span class="sxs-lookup"><span data-stu-id="64d8b-157">Customize the HttpRepl</span></span>

<span data-ttu-id="64d8b-158">As [cores](#set-color-preferences) padrão do HttpRepl podem ser personalizadas.</span><span class="sxs-lookup"><span data-stu-id="64d8b-158">The HttpRepl's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="64d8b-159">Além disso, um [editor de texto padrão](#set-the-default-text-editor) pode ser definido.</span><span class="sxs-lookup"><span data-stu-id="64d8b-159">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="64d8b-160">As preferências de HttpRepl são mantidas na sessão atual e são respeitadas em sessões futuras.</span><span class="sxs-lookup"><span data-stu-id="64d8b-160">The HttpRepl preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="64d8b-161">Depois de modificadas, as preferências são armazenadas no seguinte arquivo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-161">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="64d8b-162">Linux</span><span class="sxs-lookup"><span data-stu-id="64d8b-162">Linux</span></span>](#tab/linux)

<span data-ttu-id="64d8b-163">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="64d8b-163">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="64d8b-164">macOS</span><span class="sxs-lookup"><span data-stu-id="64d8b-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="64d8b-165">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="64d8b-165">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="64d8b-166">Windows</span><span class="sxs-lookup"><span data-stu-id="64d8b-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="64d8b-167">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="64d8b-167">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="64d8b-168">O arquivo *.httpreplprefs* é carregado na inicialização e suas alterações não são monitoradas no runtime.</span><span class="sxs-lookup"><span data-stu-id="64d8b-168">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="64d8b-169">As modificações manuais no arquivo entram em vigor somente após a reinicialização da ferramenta.</span><span class="sxs-lookup"><span data-stu-id="64d8b-169">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="64d8b-170">Exibir as configurações</span><span class="sxs-lookup"><span data-stu-id="64d8b-170">View the settings</span></span>

<span data-ttu-id="64d8b-171">Para exibir as configurações disponíveis, execute o comando `pref get`.</span><span class="sxs-lookup"><span data-stu-id="64d8b-171">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="64d8b-172">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-172">For example:</span></span>

```console
https://localhost:5001/> pref get
```

<span data-ttu-id="64d8b-173">O comando anterior exibe os pares chave-valor disponíveis:</span><span class="sxs-lookup"><span data-stu-id="64d8b-173">The preceding command displays the available key-value pairs:</span></span>

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a><span data-ttu-id="64d8b-174">Definir preferências de cores</span><span class="sxs-lookup"><span data-stu-id="64d8b-174">Set color preferences</span></span>

<span data-ttu-id="64d8b-175">No momento, as cores das respostas só são compatíveis com JSON.</span><span class="sxs-lookup"><span data-stu-id="64d8b-175">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="64d8b-176">Para personalizar a coloração padrão da ferramenta HttpRepl, localize a chave correspondente à cor a ser alterada.</span><span class="sxs-lookup"><span data-stu-id="64d8b-176">To customize the default HttpRepl tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="64d8b-177">Para ver instruções sobre como localizar as chaves, confira a seção [Exibir as configurações](#view-the-settings).</span><span class="sxs-lookup"><span data-stu-id="64d8b-177">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="64d8b-178">Por exemplo, altere o valor da chave `colors.json` de `Green` para `White`, desta forma:</span><span class="sxs-lookup"><span data-stu-id="64d8b-178">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people> pref set colors.json White
```

<span data-ttu-id="64d8b-179">Somente as [cores permitidas](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) podem ser usadas.</span><span class="sxs-lookup"><span data-stu-id="64d8b-179">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="64d8b-180">As solicitações HTTP subsequentes exibem a saída com a nova cor.</span><span class="sxs-lookup"><span data-stu-id="64d8b-180">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="64d8b-181">Quando chaves de cor específicas não estão definidas, mais chaves genéricas são consideradas.</span><span class="sxs-lookup"><span data-stu-id="64d8b-181">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="64d8b-182">Para demonstrar esse comportamento de fallback, considere o seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-182">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="64d8b-183">Se `colors.json.name` não tiver um valor, `colors.json.string` será usado.</span><span class="sxs-lookup"><span data-stu-id="64d8b-183">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="64d8b-184">Se `colors.json.string` não tiver um valor, `colors.json.literal` será usado.</span><span class="sxs-lookup"><span data-stu-id="64d8b-184">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="64d8b-185">Se `colors.json.literal` não tiver um valor, `colors.json` será usado.</span><span class="sxs-lookup"><span data-stu-id="64d8b-185">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="64d8b-186">Se `colors.json` não tiver um valor, a cor de texto padrão do shell de comando (`AllowedColors.None`) será usada.</span><span class="sxs-lookup"><span data-stu-id="64d8b-186">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="64d8b-187">Definir o tamanho do recuo</span><span class="sxs-lookup"><span data-stu-id="64d8b-187">Set indentation size</span></span>

<span data-ttu-id="64d8b-188">A personalização do tamanho do recuo da resposta só é compatível com JSON.</span><span class="sxs-lookup"><span data-stu-id="64d8b-188">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="64d8b-189">O tamanho padrão é dois espaços.</span><span class="sxs-lookup"><span data-stu-id="64d8b-189">The default size is two spaces.</span></span> <span data-ttu-id="64d8b-190">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-190">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="64d8b-191">Para alterar o tamanho padrão, defina a chave `formatting.json.indentSize`.</span><span class="sxs-lookup"><span data-stu-id="64d8b-191">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="64d8b-192">Por exemplo, para sempre usar quatro espaços:</span><span class="sxs-lookup"><span data-stu-id="64d8b-192">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="64d8b-193">As respostas subsequentes respeitarão a configuração de quatro espaços:</span><span class="sxs-lookup"><span data-stu-id="64d8b-193">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a><span data-ttu-id="64d8b-194">Definir o editor de texto padrão</span><span class="sxs-lookup"><span data-stu-id="64d8b-194">Set the default text editor</span></span>

<span data-ttu-id="64d8b-195">Por padrão, o HttpRepl não tem nenhum editor de texto configurado para uso.</span><span class="sxs-lookup"><span data-stu-id="64d8b-195">By default, the HttpRepl has no text editor configured for use.</span></span> <span data-ttu-id="64d8b-196">Para testar os métodos de API Web que exigem o corpo da solicitação HTTP, é preciso definir um editor de texto padrão.</span><span class="sxs-lookup"><span data-stu-id="64d8b-196">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="64d8b-197">A ferramenta HttpRepl inicia o editor de texto configurado para o único propósito de compor o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="64d8b-197">The HttpRepl tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="64d8b-198">Execute o seguinte comando para definir o editor de texto preferido como padrão:</span><span class="sxs-lookup"><span data-stu-id="64d8b-198">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="64d8b-199">No comando anterior, `<EXECUTABLE>` é o caminho completo para o arquivo executável do editor de texto.</span><span class="sxs-lookup"><span data-stu-id="64d8b-199">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="64d8b-200">Por exemplo, execute o seguinte comando para definir o Visual Studio Code como o editor de texto padrão:</span><span class="sxs-lookup"><span data-stu-id="64d8b-200">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="64d8b-201">Linux</span><span class="sxs-lookup"><span data-stu-id="64d8b-201">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="64d8b-202">macOS</span><span class="sxs-lookup"><span data-stu-id="64d8b-202">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="64d8b-203">Windows</span><span class="sxs-lookup"><span data-stu-id="64d8b-203">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="64d8b-204">Para iniciar o editor de texto padrão com argumentos específicos da CLI, defina a chave `editor.command.default.arguments`.</span><span class="sxs-lookup"><span data-stu-id="64d8b-204">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="64d8b-205">Por exemplo, suponha que Visual Studio Code seja o editor de texto padrão e que você sempre queira que o HttpRepl abra Visual Studio Code em uma nova sessão com as extensões desabilitadas.</span><span class="sxs-lookup"><span data-stu-id="64d8b-205">For example, assume Visual Studio Code is the default text editor and that you always want the HttpRepl to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="64d8b-206">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="64d8b-206">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> <span data-ttu-id="64d8b-207">Se o editor padrão for Visual Studio Code, você geralmente desejará passar o `-w` `--wait` argumento ou para forçar Visual Studio Code aguardar para fechar o arquivo antes de retornar.</span><span class="sxs-lookup"><span data-stu-id="64d8b-207">If your default editor is Visual Studio Code, you'll usually want to pass the `-w` or `--wait` argument to force Visual Studio Code to wait for you to close the file before returning.</span></span>

### <a name="set-the-openapi-description-search-paths"></a><span data-ttu-id="64d8b-208">Definir os caminhos de pesquisa de descrição do OpenAPI</span><span class="sxs-lookup"><span data-stu-id="64d8b-208">Set the OpenAPI Description search paths</span></span>

<span data-ttu-id="64d8b-209">Por padrão, o HttpRepl tem um conjunto de caminhos relativos que ele usa para localizar a descrição de OpenAPI ao executar o `connect` comando sem a `--openapi` opção.</span><span class="sxs-lookup"><span data-stu-id="64d8b-209">By default, the HttpRepl has a set of relative paths that it uses to find the OpenAPI description when executing the `connect` command without the `--openapi` option.</span></span> <span data-ttu-id="64d8b-210">Esses caminhos relativos são combinados com os caminhos raiz e base especificados no comando `connect`.</span><span class="sxs-lookup"><span data-stu-id="64d8b-210">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="64d8b-211">Os caminhos relativos padrão são:</span><span class="sxs-lookup"><span data-stu-id="64d8b-211">The default relative paths are:</span></span>

- <span data-ttu-id="64d8b-212">*swagger.jsem*</span><span class="sxs-lookup"><span data-stu-id="64d8b-212">*swagger.json*</span></span>
- <span data-ttu-id="64d8b-213">*Swagger/v1/swagger.jsem*</span><span class="sxs-lookup"><span data-stu-id="64d8b-213">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="64d8b-214">*/swagger.jsem*</span><span class="sxs-lookup"><span data-stu-id="64d8b-214">*/swagger.json*</span></span>
- <span data-ttu-id="64d8b-215">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="64d8b-215">*/swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="64d8b-216">*openapi.jsem*</span><span class="sxs-lookup"><span data-stu-id="64d8b-216">*openapi.json*</span></span>
- <span data-ttu-id="64d8b-217">*/openapi.jsem*</span><span class="sxs-lookup"><span data-stu-id="64d8b-217">*/openapi.json*</span></span>

<span data-ttu-id="64d8b-218">Para usar um conjunto diferente de caminhos de pesquisa em seu ambiente, defina a preferência `swagger.searchPaths`.</span><span class="sxs-lookup"><span data-stu-id="64d8b-218">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="64d8b-219">O valor precisa ser uma lista delimitada por pipes de caminhos relativos.</span><span class="sxs-lookup"><span data-stu-id="64d8b-219">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="64d8b-220">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-220">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

<span data-ttu-id="64d8b-221">Em vez de substituir a lista padrão totalmente, a lista também pode ser modificada adicionando ou removendo caminhos.</span><span class="sxs-lookup"><span data-stu-id="64d8b-221">Instead of replacing the default list altogether, the list can also be modified by adding or removing paths.</span></span>

<span data-ttu-id="64d8b-222">Para adicionar um ou mais caminhos de pesquisa à lista padrão, defina a `swagger.addToSearchPaths` preferência.</span><span class="sxs-lookup"><span data-stu-id="64d8b-222">To add one or more search paths to the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="64d8b-223">O valor precisa ser uma lista delimitada por pipes de caminhos relativos.</span><span class="sxs-lookup"><span data-stu-id="64d8b-223">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="64d8b-224">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-224">For example:</span></span>

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

<span data-ttu-id="64d8b-225">Para remover um ou mais caminhos de pesquisa da lista padrão, defina a `swagger.addToSearchPaths` preferência.</span><span class="sxs-lookup"><span data-stu-id="64d8b-225">To remove one or more search paths from the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="64d8b-226">O valor precisa ser uma lista delimitada por pipes de caminhos relativos.</span><span class="sxs-lookup"><span data-stu-id="64d8b-226">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="64d8b-227">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-227">For example:</span></span>

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="64d8b-228">Testar solicitações HTTP GET</span><span class="sxs-lookup"><span data-stu-id="64d8b-228">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="64d8b-229">Sinopse</span><span class="sxs-lookup"><span data-stu-id="64d8b-229">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="64d8b-230">Argumentos</span><span class="sxs-lookup"><span data-stu-id="64d8b-230">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="64d8b-231">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="64d8b-231">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="64d8b-232">Opções</span><span class="sxs-lookup"><span data-stu-id="64d8b-232">Options</span></span>

<span data-ttu-id="64d8b-233">As opções a seguir estão disponíveis para o comando `get`:</span><span class="sxs-lookup"><span data-stu-id="64d8b-233">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="64d8b-234">Exemplo</span><span class="sxs-lookup"><span data-stu-id="64d8b-234">Example</span></span>

<span data-ttu-id="64d8b-235">Para emitir uma solicitação HTTP GET:</span><span class="sxs-lookup"><span data-stu-id="64d8b-235">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="64d8b-236">Execute o comando `get` em um ponto de extremidade compatível:</span><span class="sxs-lookup"><span data-stu-id="64d8b-236">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> get
    ```

    <span data-ttu-id="64d8b-237">O comando anterior exibirá o seguinte formato de saída:</span><span class="sxs-lookup"><span data-stu-id="64d8b-237">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people>
    ```

1. <span data-ttu-id="64d8b-238">Recupere um registro específico passando um parâmetro para o comando `get`:</span><span class="sxs-lookup"><span data-stu-id="64d8b-238">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people> get 2
    ```

    <span data-ttu-id="64d8b-239">O comando anterior exibirá o seguinte formato de saída:</span><span class="sxs-lookup"><span data-stu-id="64d8b-239">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people>
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="64d8b-240">Testar solicitações HTTP POST</span><span class="sxs-lookup"><span data-stu-id="64d8b-240">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="64d8b-241">Sinopse</span><span class="sxs-lookup"><span data-stu-id="64d8b-241">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="64d8b-242">Argumentos</span><span class="sxs-lookup"><span data-stu-id="64d8b-242">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="64d8b-243">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="64d8b-243">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="64d8b-244">Opções</span><span class="sxs-lookup"><span data-stu-id="64d8b-244">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="64d8b-245">Exemplo</span><span class="sxs-lookup"><span data-stu-id="64d8b-245">Example</span></span>

<span data-ttu-id="64d8b-246">Para emitir uma solicitação HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="64d8b-246">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="64d8b-247">Execute o comando `post` em um ponto de extremidade compatível:</span><span class="sxs-lookup"><span data-stu-id="64d8b-247">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    <span data-ttu-id="64d8b-248">No comando anterior, o cabeçalho da solicitação HTTP `Content-Type` está configurado para indicar um tipo de mídia de corpo da solicitação do JSON.</span><span class="sxs-lookup"><span data-stu-id="64d8b-248">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="64d8b-249">O editor de texto padrão abrirá um arquivo *.tmp* com um modelo JSON que representa o corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="64d8b-249">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="64d8b-250">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-250">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="64d8b-251">Para definir o editor de texto padrão, confira a seção [Definir o editor de texto padrão](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="64d8b-251">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="64d8b-252">Modifique o modelo JSON para satisfazer os requisitos de validação de modelo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-252">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="64d8b-253">Salve o arquivo *.tmp* e feche o editor de texto.</span><span class="sxs-lookup"><span data-stu-id="64d8b-253">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="64d8b-254">A seguinte saída será exibida no shell de comando:</span><span class="sxs-lookup"><span data-stu-id="64d8b-254">The following output appears in the command shell:</span></span>

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people>
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="64d8b-255">Testar solicitações HTTP PUT</span><span class="sxs-lookup"><span data-stu-id="64d8b-255">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="64d8b-256">Sinopse</span><span class="sxs-lookup"><span data-stu-id="64d8b-256">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="64d8b-257">Argumentos</span><span class="sxs-lookup"><span data-stu-id="64d8b-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="64d8b-258">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="64d8b-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="64d8b-259">Opções</span><span class="sxs-lookup"><span data-stu-id="64d8b-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="64d8b-260">Exemplo</span><span class="sxs-lookup"><span data-stu-id="64d8b-260">Example</span></span>

<span data-ttu-id="64d8b-261">Para emitir uma solicitação HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="64d8b-261">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="64d8b-262">*Opcional* : execute o `get` comando para exibir os dados antes de modificá-los:</span><span class="sxs-lookup"><span data-stu-id="64d8b-262">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="64d8b-263">Execute o comando `put` em um ponto de extremidade compatível:</span><span class="sxs-lookup"><span data-stu-id="64d8b-263">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="64d8b-264">No comando anterior, o cabeçalho da solicitação HTTP `Content-Type` está configurado para indicar um tipo de mídia de corpo da solicitação do JSON.</span><span class="sxs-lookup"><span data-stu-id="64d8b-264">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="64d8b-265">O editor de texto padrão abrirá um arquivo *.tmp* com um modelo JSON que representa o corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="64d8b-265">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="64d8b-266">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-266">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="64d8b-267">Para definir o editor de texto padrão, confira a seção [Definir o editor de texto padrão](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="64d8b-267">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="64d8b-268">Modifique o modelo JSON para satisfazer os requisitos de validação de modelo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-268">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="64d8b-269">Salve o arquivo *.tmp* e feche o editor de texto.</span><span class="sxs-lookup"><span data-stu-id="64d8b-269">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="64d8b-270">A seguinte saída será exibida no shell de comando:</span><span class="sxs-lookup"><span data-stu-id="64d8b-270">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="64d8b-271">*Opcional* : emita um `get` comando para ver as modificações.</span><span class="sxs-lookup"><span data-stu-id="64d8b-271">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="64d8b-272">Por exemplo, se você digitar "Cherry" no editor de texto, um `get` retornará a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="64d8b-272">For example, if you typed "Cherry" in the text editor, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="64d8b-273">Testar solicitações HTTP DELETE</span><span class="sxs-lookup"><span data-stu-id="64d8b-273">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="64d8b-274">Sinopse</span><span class="sxs-lookup"><span data-stu-id="64d8b-274">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="64d8b-275">Argumentos</span><span class="sxs-lookup"><span data-stu-id="64d8b-275">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="64d8b-276">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="64d8b-276">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="64d8b-277">Opções</span><span class="sxs-lookup"><span data-stu-id="64d8b-277">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="64d8b-278">Exemplo</span><span class="sxs-lookup"><span data-stu-id="64d8b-278">Example</span></span>

<span data-ttu-id="64d8b-279">Para emitir uma solicitação HTTP DELETE:</span><span class="sxs-lookup"><span data-stu-id="64d8b-279">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="64d8b-280">*Opcional* : execute o `get` comando para exibir os dados antes de modificá-los:</span><span class="sxs-lookup"><span data-stu-id="64d8b-280">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="64d8b-281">Execute o comando `delete` em um ponto de extremidade compatível:</span><span class="sxs-lookup"><span data-stu-id="64d8b-281">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    <span data-ttu-id="64d8b-282">O comando anterior exibirá o seguinte formato de saída:</span><span class="sxs-lookup"><span data-stu-id="64d8b-282">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="64d8b-283">*Opcional* : emita um `get` comando para ver as modificações.</span><span class="sxs-lookup"><span data-stu-id="64d8b-283">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="64d8b-284">Neste exemplo, um `get` retorna a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="64d8b-284">In this example, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="64d8b-285">Testar solicitações HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="64d8b-285">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="64d8b-286">Sinopse</span><span class="sxs-lookup"><span data-stu-id="64d8b-286">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="64d8b-287">Argumentos</span><span class="sxs-lookup"><span data-stu-id="64d8b-287">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="64d8b-288">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="64d8b-288">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="64d8b-289">Opções</span><span class="sxs-lookup"><span data-stu-id="64d8b-289">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="64d8b-290">Testar solicitações HTTP HEAD</span><span class="sxs-lookup"><span data-stu-id="64d8b-290">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="64d8b-291">Sinopse</span><span class="sxs-lookup"><span data-stu-id="64d8b-291">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="64d8b-292">Argumentos</span><span class="sxs-lookup"><span data-stu-id="64d8b-292">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="64d8b-293">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="64d8b-293">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="64d8b-294">Opções</span><span class="sxs-lookup"><span data-stu-id="64d8b-294">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="64d8b-295">Testar solicitações HTTP OPTIONS</span><span class="sxs-lookup"><span data-stu-id="64d8b-295">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="64d8b-296">Sinopse</span><span class="sxs-lookup"><span data-stu-id="64d8b-296">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="64d8b-297">Argumentos</span><span class="sxs-lookup"><span data-stu-id="64d8b-297">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="64d8b-298">O parâmetro de rota, se houver, esperado pelo método de ação do controlador associado.</span><span class="sxs-lookup"><span data-stu-id="64d8b-298">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="64d8b-299">Opções</span><span class="sxs-lookup"><span data-stu-id="64d8b-299">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="64d8b-300">Configurar cabeçalhos de solicitações HTTP</span><span class="sxs-lookup"><span data-stu-id="64d8b-300">Set HTTP request headers</span></span>

<span data-ttu-id="64d8b-301">Para configurar um cabeçalho de solicitação HTTP, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="64d8b-301">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="64d8b-302">Configure embutido com a solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="64d8b-302">Set inline with the HTTP request.</span></span> <span data-ttu-id="64d8b-303">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-303">For example:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="64d8b-304">Com a abordagem anterior, cada cabeçalho de solicitação HTTP diferente exige sua própria opção `-h`.</span><span class="sxs-lookup"><span data-stu-id="64d8b-304">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="64d8b-305">Configure antes de enviar a solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="64d8b-305">Set before sending the HTTP request.</span></span> <span data-ttu-id="64d8b-306">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-306">For example:</span></span>

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    <span data-ttu-id="64d8b-307">Ao configurar o cabeçalho antes de enviar a solicitação, ele permanecerá configurado durante toda a sessão do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="64d8b-307">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="64d8b-308">Para limpar o cabeçalho, forneça um valor vazio.</span><span class="sxs-lookup"><span data-stu-id="64d8b-308">To clear the header, provide an empty value.</span></span> <span data-ttu-id="64d8b-309">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-309">For example:</span></span>
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="64d8b-310">Pontos de extremidade protegidos de teste</span><span class="sxs-lookup"><span data-stu-id="64d8b-310">Test secured endpoints</span></span>

<span data-ttu-id="64d8b-311">O HttpRepl dá suporte ao teste de pontos de extremidade protegidos das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="64d8b-311">The HttpRepl supports the testing of secured endpoints in the following ways:</span></span>

* <span data-ttu-id="64d8b-312">Por meio das credenciais padrão do usuário conectado.</span><span class="sxs-lookup"><span data-stu-id="64d8b-312">Via the default credentials of the logged in user.</span></span>
* <span data-ttu-id="64d8b-313">Por meio do uso de cabeçalhos de solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="64d8b-313">Through the use of HTTP request headers.</span></span>

### <a name="default-credentials"></a><span data-ttu-id="64d8b-314">Credenciais padrão</span><span class="sxs-lookup"><span data-stu-id="64d8b-314">Default credentials</span></span>

<span data-ttu-id="64d8b-315">Considere uma API Web que você está testando no IIS e protegida com a autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="64d8b-315">Consider a web API you're testing that's hosted in IIS and secured with Windows authentication.</span></span> <span data-ttu-id="64d8b-316">Você deseja que as credenciais do usuário que executa a ferramenta fluam para os pontos de extremidade HTTP que estão sendo testados.</span><span class="sxs-lookup"><span data-stu-id="64d8b-316">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="64d8b-317">Para passar as credenciais padrão do usuário conectado:</span><span class="sxs-lookup"><span data-stu-id="64d8b-317">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="64d8b-318">Defina a `httpClient.useDefaultCredentials` preferência como `true` :</span><span class="sxs-lookup"><span data-stu-id="64d8b-318">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="64d8b-319">Saia e reinicie a ferramenta antes de enviar outra solicitação para a API da Web.</span><span class="sxs-lookup"><span data-stu-id="64d8b-319">Exit and restart the tool before sending another request to the web API.</span></span>
 
### <a name="default-proxy-credentials"></a><span data-ttu-id="64d8b-320">Credenciais de proxy padrão</span><span class="sxs-lookup"><span data-stu-id="64d8b-320">Default proxy credentials</span></span>

<span data-ttu-id="64d8b-321">Considere um cenário no qual a API Web que você está testando está atrás de um proxy protegido com a autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="64d8b-321">Consider a scenario in which the web API you're testing is behind a proxy secured with Windows authentication.</span></span> <span data-ttu-id="64d8b-322">Você deseja que as credenciais do usuário que executa a ferramenta fluam para o proxy.</span><span class="sxs-lookup"><span data-stu-id="64d8b-322">You want the credentials of the user running the tool to flow to the proxy.</span></span> <span data-ttu-id="64d8b-323">Para passar as credenciais padrão do usuário conectado:</span><span class="sxs-lookup"><span data-stu-id="64d8b-323">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="64d8b-324">Defina a `httpClient.proxy.useDefaultCredentials` preferência como `true` :</span><span class="sxs-lookup"><span data-stu-id="64d8b-324">Set the `httpClient.proxy.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. <span data-ttu-id="64d8b-325">Saia e reinicie a ferramenta antes de enviar outra solicitação para a API da Web.</span><span class="sxs-lookup"><span data-stu-id="64d8b-325">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="64d8b-326">Cabeçalhos de solicitação HTTP</span><span class="sxs-lookup"><span data-stu-id="64d8b-326">HTTP request headers</span></span>

<span data-ttu-id="64d8b-327">Entre os exemplos de esquemas de autenticação e autorização com suporte estão:</span><span class="sxs-lookup"><span data-stu-id="64d8b-327">Examples of supported authentication and authorization schemes include:</span></span>

* <span data-ttu-id="64d8b-328">basic authentication</span><span class="sxs-lookup"><span data-stu-id="64d8b-328">basic authentication</span></span>
* <span data-ttu-id="64d8b-329">Tokens de portador JWT</span><span class="sxs-lookup"><span data-stu-id="64d8b-329">JWT bearer tokens</span></span>
* <span data-ttu-id="64d8b-330">autenticação resumida</span><span class="sxs-lookup"><span data-stu-id="64d8b-330">digest authentication</span></span>

<span data-ttu-id="64d8b-331">Por exemplo, você pode enviar um token de portador para um ponto de extremidade com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="64d8b-331">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="64d8b-332">Para acessar um ponto de extremidade hospedado no Azure ou usar a [API REST do Azure](/rest/api/azure/), você precisa de um token de portador.</span><span class="sxs-lookup"><span data-stu-id="64d8b-332">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="64d8b-333">Use as etapas a seguir para obter um token de portador para sua assinatura do Azure por meio do [CLI do Azure](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="64d8b-333">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="64d8b-334">O HttpRepl define o token de portador em um cabeçalho de solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="64d8b-334">The HttpRepl sets the bearer token in an HTTP request header.</span></span> <span data-ttu-id="64d8b-335">Uma lista de aplicativos Web do serviço de Azure App é recuperada.</span><span class="sxs-lookup"><span data-stu-id="64d8b-335">A list of Azure App Service Web Apps is retrieved.</span></span>

1. <span data-ttu-id="64d8b-336">Entrar no Azure:</span><span class="sxs-lookup"><span data-stu-id="64d8b-336">Sign in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="64d8b-337">Obtenha sua ID de assinatura com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="64d8b-337">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="64d8b-338">Copie sua ID de assinatura e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="64d8b-338">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="64d8b-339">Obtenha seu token de portador com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="64d8b-339">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="64d8b-340">Conecte-se à API REST do Azure por meio do HttpRepl:</span><span class="sxs-lookup"><span data-stu-id="64d8b-340">Connect to the Azure REST API via the HttpRepl:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="64d8b-341">Defina o `Authorization` cabeçalho da solicitação http:</span><span class="sxs-lookup"><span data-stu-id="64d8b-341">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="64d8b-342">Navegue até a assinatura:</span><span class="sxs-lookup"><span data-stu-id="64d8b-342">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="64d8b-343">Obtenha uma lista dos aplicativos Web do serviço de Azure App da sua assinatura:</span><span class="sxs-lookup"><span data-stu-id="64d8b-343">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="64d8b-344">A seguinte resposta é exibida:</span><span class="sxs-lookup"><span data-stu-id="64d8b-344">The following response is displayed:</span></span>

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="64d8b-345">Alternar exibição da solicitação HTTP</span><span class="sxs-lookup"><span data-stu-id="64d8b-345">Toggle HTTP request display</span></span>

<span data-ttu-id="64d8b-346">Por padrão, a exibição da solicitação HTTP que está sendo enviada é suprimida.</span><span class="sxs-lookup"><span data-stu-id="64d8b-346">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="64d8b-347">É possível alterar a configuração correspondente durante a sessão do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="64d8b-347">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="64d8b-348">Habilitar a exibição da solicitação</span><span class="sxs-lookup"><span data-stu-id="64d8b-348">Enable request display</span></span>

<span data-ttu-id="64d8b-349">Exiba a solicitação HTTP que está sendo enviada executando o comando `echo on`.</span><span class="sxs-lookup"><span data-stu-id="64d8b-349">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="64d8b-350">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-350">For example:</span></span>

```console
https://localhost:5001/people> echo on
Request echoing is on
```

<span data-ttu-id="64d8b-351">As solicitações HTTP subsequentes na sessão atual exibem os cabeçalhos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="64d8b-351">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="64d8b-352">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-352">For example:</span></span>

```console
https://localhost:5001/people> post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people>
```

### <a name="disable-request-display"></a><span data-ttu-id="64d8b-353">Desabilitar a exibição da solicitação</span><span class="sxs-lookup"><span data-stu-id="64d8b-353">Disable request display</span></span>

<span data-ttu-id="64d8b-354">Suprima a exibição da solicitação HTTP que está sendo enviada executando o comando `echo off`.</span><span class="sxs-lookup"><span data-stu-id="64d8b-354">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="64d8b-355">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-355">For example:</span></span>

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="64d8b-356">Executar um script</span><span class="sxs-lookup"><span data-stu-id="64d8b-356">Run a script</span></span>

<span data-ttu-id="64d8b-357">Se você executar com frequência o mesmo conjunto de comandos HttpRepl, considere armazená-los em um arquivo de texto.</span><span class="sxs-lookup"><span data-stu-id="64d8b-357">If you frequently execute the same set of HttpRepl commands, consider storing them in a text file.</span></span> <span data-ttu-id="64d8b-358">Os comandos no arquivo assumem a mesma forma como comandos executados manualmente na linha de comando.</span><span class="sxs-lookup"><span data-stu-id="64d8b-358">Commands in the file take the same form as commands executed manually on the command line.</span></span> <span data-ttu-id="64d8b-359">Os comandos podem ser executados em um modo em lote usando o comando `run`.</span><span class="sxs-lookup"><span data-stu-id="64d8b-359">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="64d8b-360">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-360">For example:</span></span>

1. <span data-ttu-id="64d8b-361">Crie um arquivo de texto com um conjunto de comandos delimitados por nova linha.</span><span class="sxs-lookup"><span data-stu-id="64d8b-361">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="64d8b-362">Para ilustrar, considere um arquivo *people-script.txt* com os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="64d8b-362">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="64d8b-363">Execute o comando `run`, passando o caminho do arquivo de texto.</span><span class="sxs-lookup"><span data-stu-id="64d8b-363">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="64d8b-364">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="64d8b-364">For example:</span></span>

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="64d8b-365">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="64d8b-365">The following output appears:</span></span>

    ```console
    https://localhost:5001/> set base https://localhost:5001
    Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/> ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/> cd People
    /People    [get|post]
    
    https://localhost:5001/People> ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People> get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People>
    ```

## <a name="clear-the-output"></a><span data-ttu-id="64d8b-366">Limpar a saída</span><span class="sxs-lookup"><span data-stu-id="64d8b-366">Clear the output</span></span>

<span data-ttu-id="64d8b-367">Para remover toda a saída gravada no Shell de comando pela ferramenta HttpRepl, execute `clear` o `cls` comando ou.</span><span class="sxs-lookup"><span data-stu-id="64d8b-367">To remove all output written to the command shell by the HttpRepl tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="64d8b-368">Para ilustrar, imagine que o shell de comando contém a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="64d8b-368">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)> set base "https://localhost:5001"
Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/> ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="64d8b-369">Execute o comando a seguir para limpar a saída:</span><span class="sxs-lookup"><span data-stu-id="64d8b-369">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/> clear
```

<span data-ttu-id="64d8b-370">Após a execução o comando anterior, o shell de comando conterá somente a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="64d8b-370">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a><span data-ttu-id="64d8b-371">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="64d8b-371">Additional resources</span></span>

* [<span data-ttu-id="64d8b-372">Solicitações da API REST</span><span class="sxs-lookup"><span data-stu-id="64d8b-372">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="64d8b-373">Repositório GitHub do HttpRepl</span><span class="sxs-lookup"><span data-stu-id="64d8b-373">HttpRepl GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
