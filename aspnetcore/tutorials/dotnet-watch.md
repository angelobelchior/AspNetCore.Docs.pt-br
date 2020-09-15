---
title: Desenvolver aplicativos ASP.NET Core usando um observador de arquivo
author: rick-anderson
description: Este tutorial demonstra como instalar e usar a ferramenta observador de arquivo (observação de dotnet) da CLI do .NET Core em um aplicativo do ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
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
uid: tutorials/dotnet-watch
ms.openlocfilehash: 3569e9440b8e431ec0e5357e548af2e3783481ac
ms.sourcegitcommit: 422e02bad384775bfe19a90910737340ad106c5b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90083447"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="75189-103">Desenvolver aplicativos ASP.NET Core usando um observador de arquivo</span><span class="sxs-lookup"><span data-stu-id="75189-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="75189-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="75189-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="75189-105">`dotnet watch` é uma ferramenta que executa um comando [CLI do .NET Core](/dotnet/core/tools) quando os arquivos de origem são alterados.</span><span class="sxs-lookup"><span data-stu-id="75189-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="75189-106">Por exemplo, uma alteração de arquivo pode disparar uma compilação, execução de teste ou uma implantação.</span><span class="sxs-lookup"><span data-stu-id="75189-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="75189-107">Este tutorial usa um aplicativo de API Web existente com dois pontos de extremidade: um que retorna uma soma e outro que retorna um produto.</span><span class="sxs-lookup"><span data-stu-id="75189-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="75189-108">O método de produto tem um bug, que é corrigido neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="75189-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="75189-109">Baixe o [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="75189-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="75189-110">Ele consiste em dois projetos: *WebApp* (uma API Web ASP.NET Core) e *WebAppTests* (testes de unidade para a API Web).</span><span class="sxs-lookup"><span data-stu-id="75189-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="75189-111">Em um shell de comando, navegue até a pasta *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="75189-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="75189-112">Execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="75189-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="75189-113">`dotnet run --project <PROJECT>` pode ser usado para especificar um projeto a ser executado.</span><span class="sxs-lookup"><span data-stu-id="75189-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="75189-114">Por exemplo, a execução de `dotnet run --project WebApp` da raiz do aplicativo de exemplo também executará o projeto *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="75189-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="75189-115">O resultado do console mostra mensagens semelhantes à seguinte (indicando que o aplicativo está em execução e aguarda solicitações):</span><span class="sxs-lookup"><span data-stu-id="75189-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="75189-116">Em um navegador web, navegue até `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="75189-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="75189-117">Você deve ver o resultado de `9`.</span><span class="sxs-lookup"><span data-stu-id="75189-117">You should see the result of `9`.</span></span>

<span data-ttu-id="75189-118">Navegue para o API do produto (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="75189-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="75189-119">Ele retorna `9`, não `20`, conforme o esperado.</span><span class="sxs-lookup"><span data-stu-id="75189-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="75189-120">Esse problema é corrigido mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="75189-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="75189-121">Adicionar `dotnet watch` a um projeto</span><span class="sxs-lookup"><span data-stu-id="75189-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="75189-122">A ferramenta de observador de arquivo `dotnet watch` está incluída com a versão 2.1.300 do SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="75189-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="75189-123">As etapas a seguir são necessárias ao usar uma versão anterior do SDK do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="75189-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="75189-124">Adicionar uma referência ao pacote de `Microsoft.DotNet.Watcher.Tools` para o arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="75189-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="75189-125">Instale o pacote `Microsoft.DotNet.Watcher.Tools` executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="75189-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="75189-126">Executar os comandos da CLI do .NET Core usando `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="75189-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="75189-127">Qualquer [comando da CLI do .NET Core](/dotnet/core/tools#cli-commands) pode ser executado com `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="75189-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="75189-128">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="75189-128">For example:</span></span>

| <span data-ttu-id="75189-129">Comando</span><span class="sxs-lookup"><span data-stu-id="75189-129">Command</span></span> | <span data-ttu-id="75189-130">Comando com inspeção</span><span class="sxs-lookup"><span data-stu-id="75189-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="75189-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="75189-131">dotnet run</span></span> | <span data-ttu-id="75189-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="75189-132">dotnet watch run</span></span> |
| <span data-ttu-id="75189-133">execução de dotnet-f netcoreapp 3.1</span><span class="sxs-lookup"><span data-stu-id="75189-133">dotnet run -f netcoreapp3.1</span></span> | <span data-ttu-id="75189-134">execução do relógio dotnet-f netcoreapp 3.1</span><span class="sxs-lookup"><span data-stu-id="75189-134">dotnet watch run -f netcoreapp3.1</span></span> |
| <span data-ttu-id="75189-135">dotnet Run-f netcoreapp 3.1----arg1</span><span class="sxs-lookup"><span data-stu-id="75189-135">dotnet run -f netcoreapp3.1 -- --arg1</span></span> | <span data-ttu-id="75189-136">execução do relógio dotnet-f netcoreapp 3.1----arg1</span><span class="sxs-lookup"><span data-stu-id="75189-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span></span> |
| <span data-ttu-id="75189-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="75189-137">dotnet test</span></span> | <span data-ttu-id="75189-138">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="75189-138">dotnet watch test</span></span> |

<span data-ttu-id="75189-139">Executar `dotnet watch run` na pasta *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="75189-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="75189-140">O resultado do console indica que `watch` foi iniciado.</span><span class="sxs-lookup"><span data-stu-id="75189-140">The console output indicates `watch` has started.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="75189-141">A execução `dotnet watch run` em um aplicativo Web inicia um navegador que navega para a URL do aplicativo quando estiver pronto.</span><span class="sxs-lookup"><span data-stu-id="75189-141">Running `dotnet watch run` on a web app launches a browser that navigates to the app's URL once ready.</span></span> <span data-ttu-id="75189-142">`dotnet watch` faz isso lendo a saída do console do aplicativo e aguardando a mensagem pronta exibida pelo <xref:Microsoft.AspNetCore.WebHost> .</span><span class="sxs-lookup"><span data-stu-id="75189-142">`dotnet watch` does this by reading the app's console output and waiting for the the ready message displayed by <xref:Microsoft.AspNetCore.WebHost>.</span></span>

<span data-ttu-id="75189-143">`dotnet watch` atualiza o navegador quando detecta alterações em arquivos observados.</span><span class="sxs-lookup"><span data-stu-id="75189-143">`dotnet watch` refreshes the browser when it detects changes to watched files.</span></span> <span data-ttu-id="75189-144">Para fazer isso, o comando Watch injeta um middleware para o aplicativo que modifica as respostas HTML criadas pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75189-144">To do this, the watch command injects a middleware to the app that modifies HTML responses created by the app.</span></span> <span data-ttu-id="75189-145">O middleware adiciona um bloco de script JavaScript à página que permite `dotnet watch` instruir o navegador a ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="75189-145">The middleware adds a JavaScript script block to the page that allows `dotnet watch` to instruct the browser to refresh.</span></span> <span data-ttu-id="75189-146">Atualmente, as alterações em todos os arquivos observados, incluindo conteúdo estático, como arquivos *. html* e *. css* , fazem com que o aplicativo seja recriado.</span><span class="sxs-lookup"><span data-stu-id="75189-146">Currently, changes to all watched files, including static content such as *.html* and *.css* files cause the app to be rebuilt.</span></span>

<span data-ttu-id="75189-147">`dotnet watch`:</span><span class="sxs-lookup"><span data-stu-id="75189-147">`dotnet watch`:</span></span>

* <span data-ttu-id="75189-148">Observa apenas os arquivos que afetam as compilações por padrão.</span><span class="sxs-lookup"><span data-stu-id="75189-148">Only watches files that impact builds by default.</span></span>
* <span data-ttu-id="75189-149">Os arquivos observados adicionalmente (por meio da configuração) ainda resultam em uma compilação em andamento.</span><span class="sxs-lookup"><span data-stu-id="75189-149">Any additionally watched files (via configuration) still results in a build taking place.</span></span>

<span data-ttu-id="75189-150">Para obter mais informações sobre a configuração, consulte [dotnet – configuração do Watch](#dotnet-watch-configuration) neste documento</span><span class="sxs-lookup"><span data-stu-id="75189-150">For more information on configuration, see [dotnet-watch configuration](#dotnet-watch-configuration) in this document</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="75189-151">`dotnet watch --project <PROJECT>` pode ser usado para especificar um projeto a ser observado.</span><span class="sxs-lookup"><span data-stu-id="75189-151">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="75189-152">Por exemplo, a execução de `dotnet watch --project WebApp run` da raiz do aplicativo de exemplo também executará e observará o projeto *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="75189-152">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="75189-153">Fazer alterações com `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="75189-153">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="75189-154">Verifique se `dotnet watch` está em execução.</span><span class="sxs-lookup"><span data-stu-id="75189-154">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="75189-155">Corrija o bug no método `Product` do *MathController.cs* para que ele retorne o produto e não a soma:</span><span class="sxs-lookup"><span data-stu-id="75189-155">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="75189-156">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="75189-156">Save the file.</span></span> <span data-ttu-id="75189-157">O resultado do console indica que o `dotnet watch` detectou uma alteração de arquivo e reiniciou o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="75189-157">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="75189-158">Verifique se `http://localhost:<port number>/api/math/product?a=4&b=5` retorna o resultado correto.</span><span class="sxs-lookup"><span data-stu-id="75189-158">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="75189-159">Executar testes usando o `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="75189-159">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="75189-160">Altere o método `Product` de *MathController.cs* de volta para retornar a soma.</span><span class="sxs-lookup"><span data-stu-id="75189-160">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="75189-161">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="75189-161">Save the file.</span></span>
1. <span data-ttu-id="75189-162">Em um shell de comando, navegue até a pasta *WebAppTests*.</span><span class="sxs-lookup"><span data-stu-id="75189-162">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="75189-163">Execute [dotnet restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="75189-163">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="75189-164">Execute `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="75189-164">Run `dotnet watch test`.</span></span> <span data-ttu-id="75189-165">Seu resultado indica que um teste falhou e que o observador está aguardando as alterações de arquivo:</span><span class="sxs-lookup"><span data-stu-id="75189-165">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="75189-166">Corrija o código do método `Product` para que ele retorne o produto.</span><span class="sxs-lookup"><span data-stu-id="75189-166">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="75189-167">Salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="75189-167">Save the file.</span></span>

<span data-ttu-id="75189-168">`dotnet watch` detecta a alteração de arquivo e executa os testes novamente.</span><span class="sxs-lookup"><span data-stu-id="75189-168">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="75189-169">O resultado do console indica a aprovação nos testes.</span><span class="sxs-lookup"><span data-stu-id="75189-169">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="75189-170">Personalizar lista de arquivos a observar</span><span class="sxs-lookup"><span data-stu-id="75189-170">Customize files list to watch</span></span>

<span data-ttu-id="75189-171">Por padrão, o `dotnet-watch` controla todos os arquivos que correspondem aos seguintes padrões glob:</span><span class="sxs-lookup"><span data-stu-id="75189-171">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="75189-172">Mais itens podem ser adicionados à lista de inspeção editando o arquivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="75189-172">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="75189-173">Os itens podem ser especificados individualmente ou usando padrões glob.</span><span class="sxs-lookup"><span data-stu-id="75189-173">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="75189-174">Recusa de arquivos a serem observados</span><span class="sxs-lookup"><span data-stu-id="75189-174">Opt-out of files to be watched</span></span>

<span data-ttu-id="75189-175">`dotnet-watch` pode ser configurado para ignorar as configurações padrão.</span><span class="sxs-lookup"><span data-stu-id="75189-175">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="75189-176">Para ignorar arquivos específicos, adicione o atributo `Watch="false"` à definição de um item no arquivo *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="75189-176">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="75189-177">Projetos de inspeção personalizados</span><span class="sxs-lookup"><span data-stu-id="75189-177">Custom watch projects</span></span>

<span data-ttu-id="75189-178">`dotnet-watch` não é restrito a projetos C#.</span><span class="sxs-lookup"><span data-stu-id="75189-178">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="75189-179">Projetos de inspeção personalizados podem ser criados para lidar com cenários diferentes.</span><span class="sxs-lookup"><span data-stu-id="75189-179">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="75189-180">Considere o layout de projeto a seguir:</span><span class="sxs-lookup"><span data-stu-id="75189-180">Consider the following project layout:</span></span>

* <span data-ttu-id="75189-181">**testar**</span><span class="sxs-lookup"><span data-stu-id="75189-181">**test/**</span></span>
  * <span data-ttu-id="75189-182">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="75189-182">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="75189-183">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="75189-183">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="75189-184">Se a meta é observar ambos os projetos, crie um arquivo de projeto personalizados configurado para observar os dois projetos:</span><span class="sxs-lookup"><span data-stu-id="75189-184">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="75189-185">Para iniciar a observação de arquivo em ambos os projetos, mude para a pasta de *teste*.</span><span class="sxs-lookup"><span data-stu-id="75189-185">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="75189-186">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="75189-186">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="75189-187">O VSTest é executado quando há qualquer mudança de arquivo no projeto de teste.</span><span class="sxs-lookup"><span data-stu-id="75189-187">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-configuration"></a><span data-ttu-id="75189-188">dotnet-configuração de inspeção</span><span class="sxs-lookup"><span data-stu-id="75189-188">dotnet-watch configuration</span></span>

<span data-ttu-id="75189-189">Algumas opções de configuração podem ser passadas para `dotnet watch` por meio de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="75189-189">Some configuration options can be passed to `dotnet watch` through environment variables.</span></span> <span data-ttu-id="75189-190">As variáveis disponíveis são:</span><span class="sxs-lookup"><span data-stu-id="75189-190">The available variables are:</span></span>

| <span data-ttu-id="75189-191">Setting</span><span class="sxs-lookup"><span data-stu-id="75189-191">Setting</span></span>  | <span data-ttu-id="75189-192">Descrição</span><span class="sxs-lookup"><span data-stu-id="75189-192">Description</span></span> |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | <span data-ttu-id="75189-193">Se definido como "1" ou "true", `dotnet watch` usa um observador de arquivo de sondagem em vez de CoreFx `FileSystemWatcher` .</span><span class="sxs-lookup"><span data-stu-id="75189-193">If set to "1" or "true", `dotnet watch` uses a polling file watcher instead of CoreFx's `FileSystemWatcher`.</span></span> <span data-ttu-id="75189-194">Usado ao assistir arquivos em compartilhamentos de rede ou volumes montados no Docker.</span><span class="sxs-lookup"><span data-stu-id="75189-194">Used when watching files on network shares or Docker mounted volumes.</span></span>                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | <span data-ttu-id="75189-195">Por padrão, `dotnet watch` o otimiza a compilação ao evitar determinadas operações, como executar a restauração ou reavaliar o conjunto de arquivos observados em cada alteração de arquivo.</span><span class="sxs-lookup"><span data-stu-id="75189-195">By default, `dotnet watch` optimizes the build by avoiding certain operations such as running restore or re-evaluating the set of watched files on every file change.</span></span> <span data-ttu-id="75189-196">Se definido como "1" ou "verdadeiro", essas otimizações serão desabilitadas.</span><span class="sxs-lookup"><span data-stu-id="75189-196">If set to "1" or "true",  these optimizations are disabled.</span></span> |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | <span data-ttu-id="75189-197">`dotnet watch run` tenta iniciar navegadores para aplicativos Web com `launchBrowser` configurado no *launchSettings.jsno*.</span><span class="sxs-lookup"><span data-stu-id="75189-197">`dotnet watch run` attempts to launch browsers for web apps with `launchBrowser` configured in *launchSettings.json*.</span></span> <span data-ttu-id="75189-198">Se definido como "1" ou "true", esse comportamento é suprimido.</span><span class="sxs-lookup"><span data-stu-id="75189-198">If set to "1" or "true", this behavior is suppressed.</span></span> |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | <span data-ttu-id="75189-199">`dotnet watch run` tenta atualizar os navegadores quando detecta alterações de arquivo.</span><span class="sxs-lookup"><span data-stu-id="75189-199">`dotnet watch run` attempts to refresh browsers when it detects file changes.</span></span> <span data-ttu-id="75189-200">Se definido como "1" ou "true", esse comportamento é suprimido.</span><span class="sxs-lookup"><span data-stu-id="75189-200">If set to "1" or "true", this behavior is suppressed.</span></span> <span data-ttu-id="75189-201">Esse comportamento também será suprimido se `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` estiver definido.</span><span class="sxs-lookup"><span data-stu-id="75189-201">This behavior is also suppressed if `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` is set.</span></span> |

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="75189-202">`dotnet-watch` no GitHub</span><span class="sxs-lookup"><span data-stu-id="75189-202">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="75189-203">`dotnet-watch` faz parte do [repositório dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)do github.</span><span class="sxs-lookup"><span data-stu-id="75189-203">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
