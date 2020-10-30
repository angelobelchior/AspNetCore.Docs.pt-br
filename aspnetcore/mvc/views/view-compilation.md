---
title: ':::no-loc(Razor)::: compilação de arquivo no ASP.NET Core'
author: rick-anderson
description: 'Saiba como a compilação de :::no-loc(Razor)::: arquivos ocorre em um aplicativo ASP.NET Core.'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
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
uid: mvc/views/view-compilation
ms.openlocfilehash: 77ca96b329136ee044ab6fc5f6b5ebb5b67fe64c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059072"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a><span data-ttu-id="b2d1e-103">:::no-loc(Razor)::: compilação de arquivo no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b2d1e-103">:::no-loc(Razor)::: file compilation in ASP.NET Core</span></span>

<span data-ttu-id="b2d1e-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b2d1e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="b2d1e-105">:::no-loc(Razor):::arquivos com uma extensão *. cshtml* são compilados em tempo de compilação e publicação usando o [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="b2d1e-105">:::no-loc(Razor)::: files with a *.cshtml* extension are compiled at both build and publish time using the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="b2d1e-106">A compilação em tempo de execução pode ser habilitada opcionalmente Configurando seu projeto.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="b2d1e-107">:::no-loc(Razor)::: ocorrida</span><span class="sxs-lookup"><span data-stu-id="b2d1e-107">:::no-loc(Razor)::: compilation</span></span>

<span data-ttu-id="b2d1e-108">A compilação de arquivos em tempo de compilação e em tempo de publicação :::no-loc(Razor)::: é habilitada por padrão pelo :::no-loc(Razor)::: SDK.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-108">Build-time and publish-time compilation of :::no-loc(Razor)::: files is enabled by default by the :::no-loc(Razor)::: SDK.</span></span> <span data-ttu-id="b2d1e-109">Quando habilitada, a compilação de tempo de execução complementa a compilação em tempo de compilação, permitindo que :::no-loc(Razor)::: os arquivos sejam atualizados se eles forem editados.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-109">When enabled, runtime compilation complements build-time compilation, allowing :::no-loc(Razor)::: files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="b2d1e-110">Habilitar a compilação em tempo de execução na criação do projeto</span><span class="sxs-lookup"><span data-stu-id="b2d1e-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="b2d1e-111">As :::no-loc(Razor)::: páginas e os modelos de projeto MVC incluem uma opção para habilitar a compilação em tempo de execução quando o projeto é criado.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-111">The :::no-loc(Razor)::: Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="b2d1e-112">Essa opção tem suporte no ASP.NET Core 3,1 e posterior.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b2d1e-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b2d1e-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b2d1e-114">Na caixa de diálogo **criar um novo aplicativo web ASP.NET Core** :</span><span class="sxs-lookup"><span data-stu-id="b2d1e-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="b2d1e-115">Selecione o modelo de **projeto do aplicativo Web ou do** **aplicativo Web (Model-View-Controller)** .</span><span class="sxs-lookup"><span data-stu-id="b2d1e-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="b2d1e-116">Marque a caixa de seleção **habilitar :::no-loc(Razor)::: compilação de tempo de execução** .</span><span class="sxs-lookup"><span data-stu-id="b2d1e-116">Select the **Enable :::no-loc(Razor)::: runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b2d1e-117">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="b2d1e-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b2d1e-118">Use a `-rrc` `--razor-runtime-compilation` opção de modelo ou.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="b2d1e-119">Por exemplo, o comando a seguir cria um novo :::no-loc(Razor)::: projeto de páginas com compilação em tempo de execução habilitada:</span><span class="sxs-lookup"><span data-stu-id="b2d1e-119">For example, the following command creates a new :::no-loc(Razor)::: Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="b2d1e-120">Habilitar a compilação em tempo de execução em um projeto existente</span><span class="sxs-lookup"><span data-stu-id="b2d1e-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="b2d1e-121">Para habilitar a compilação em tempo de execução para todos os ambientes em um projeto existente:</span><span class="sxs-lookup"><span data-stu-id="b2d1e-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="b2d1e-122">Instale o [Microsoft. AspNetCore. Mvc. :::no-loc(Razor)::: . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) Pacote NuGet do RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-122">Install the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="b2d1e-123">Atualize o método do projeto `Startup.ConfigureServices` para incluir uma chamada para <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="b2d1e-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*>.</span></span> <span data-ttu-id="b2d1e-124">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b2d1e-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Add:::no-loc(Razor):::Pages()
            .Add:::no-loc(Razor):::RuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="b2d1e-125">Habilitar condicionalmente a compilação em tempo de execução em um projeto existente</span><span class="sxs-lookup"><span data-stu-id="b2d1e-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="b2d1e-126">A compilação em tempo de execução pode ser habilitada de modo que esteja disponível apenas para desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="b2d1e-127">Habilitar condicionalmente dessa maneira garante que a saída publicada:</span><span class="sxs-lookup"><span data-stu-id="b2d1e-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="b2d1e-128">Usa exibições compiladas.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-128">Uses compiled views.</span></span>
* <span data-ttu-id="b2d1e-129">Não habilita os inspetores de arquivo na produção.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="b2d1e-130">Para habilitar a compilação em tempo de execução somente no ambiente de desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="b2d1e-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="b2d1e-131">Instale o [Microsoft. AspNetCore. Mvc. :::no-loc(Razor)::: . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) Pacote NuGet do RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-131">Install the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="b2d1e-132">Modifique a seção de perfil de inicialização `environmentVariables` no *launchSettings.jsem* :</span><span class="sxs-lookup"><span data-stu-id="b2d1e-132">Modify the launch profile `environmentVariables` section in *launchSettings.json* :</span></span>
    * <span data-ttu-id="b2d1e-133">Verifique se `ASPNETCORE_ENVIRONMENT` está definido como `"Development"` .</span><span class="sxs-lookup"><span data-stu-id="b2d1e-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="b2d1e-134">Defina `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` como `"Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation"`.</span></span>

<span data-ttu-id="b2d1e-135">No exemplo a seguir, a compilação em tempo de execução está habilitada no ambiente de desenvolvimento para os `IIS Express` `:::no-loc(Razor):::PagesApp` perfis de inicialização e:</span><span class="sxs-lookup"><span data-stu-id="b2d1e-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `:::no-loc(Razor):::PagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="b2d1e-136">Nenhuma alteração de código é necessária na classe do projeto `Startup` .</span><span class="sxs-lookup"><span data-stu-id="b2d1e-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="b2d1e-137">Em tempo de execução, ASP.NET Core procura um [atributo HostingStartup no nível de assembly](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) no `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="b2d1e-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation`.</span></span> <span data-ttu-id="b2d1e-138">O `HostingStartup` atributo especifica o código de inicialização do aplicativo a ser executado.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="b2d1e-139">Esse código de inicialização habilita a compilação em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a><span data-ttu-id="b2d1e-140">Habilitar a compilação em tempo de execução para uma :::no-loc(Razor)::: biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="b2d1e-140">Enable runtime compilation for a :::no-loc(Razor)::: Class Library</span></span>

<span data-ttu-id="b2d1e-141">Considere um cenário no qual um :::no-loc(Razor)::: projeto de páginas referencia uma [ :::no-loc(Razor)::: biblioteca de classes (RCL)](xref:razor-pages/ui-class) chamada *MyClassLib* .</span><span class="sxs-lookup"><span data-stu-id="b2d1e-141">Consider a scenario in which a :::no-loc(Razor)::: Pages project references a [:::no-loc(Razor)::: Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib* .</span></span> <span data-ttu-id="b2d1e-142">O RCL contém um arquivo *_Layout. cshtml* que todos os projetos de página e MVC da sua equipe :::no-loc(Razor)::: consomem.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and :::no-loc(Razor)::: Pages projects consume.</span></span> <span data-ttu-id="b2d1e-143">Você deseja habilitar a compilação em tempo de execução para o arquivo *_Layout. cshtml* nesse RCL.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="b2d1e-144">Faça as seguintes alterações no :::no-loc(Razor)::: projeto de páginas:</span><span class="sxs-lookup"><span data-stu-id="b2d1e-144">Make the following changes in the :::no-loc(Razor)::: Pages project:</span></span>

1. <span data-ttu-id="b2d1e-145">Habilite a compilação em tempo de execução com as instruções em [habilitar condicionalmente a compilação em tempo de execução em um projeto existente](#conditionally-enable-runtime-compilation-in-an-existing-project).</span><span class="sxs-lookup"><span data-stu-id="b2d1e-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="b2d1e-146">Configure as opções de compilação de tempo de execução em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b2d1e-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="b2d1e-147">No código anterior, um caminho absoluto para o *MyClassLib* RCL é construído.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="b2d1e-148">A [API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) é usada para localizar diretórios e arquivos nesse caminho absoluto.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="b2d1e-149">Por fim, a `PhysicalFileProvider` instância é adicionada a uma coleção de provedores de arquivos, que permite o acesso aos arquivos *. cshtml* do RCL.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b2d1e-150">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b2d1e-150">Additional resources</span></span>

* <span data-ttu-id="b2d1e-151">Propriedades [ :::no-loc(Razor)::: CompileOnBuild e :::no-loc(Razor)::: CompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="b2d1e-151">[:::no-loc(Razor):::CompileOnBuild and :::no-loc(Razor):::CompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="b2d1e-152">:::no-loc(Razor):::arquivos com uma extensão *. cshtml* são compilados em tempo de compilação e publicação usando o [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="b2d1e-152">:::no-loc(Razor)::: files with a *.cshtml* extension are compiled at both build and publish time using the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="b2d1e-153">A compilação do runtime pode ser opcionalmente habilitada através da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="b2d1e-154">:::no-loc(Razor)::: ocorrida</span><span class="sxs-lookup"><span data-stu-id="b2d1e-154">:::no-loc(Razor)::: compilation</span></span>

<span data-ttu-id="b2d1e-155">A compilação de arquivos em tempo de compilação e em tempo de publicação :::no-loc(Razor)::: é habilitada por padrão pelo :::no-loc(Razor)::: SDK.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-155">Build-time and publish-time compilation of :::no-loc(Razor)::: files is enabled by default by the :::no-loc(Razor)::: SDK.</span></span> <span data-ttu-id="b2d1e-156">Quando habilitada, a compilação de tempo de execução complementa a compilação em tempo de compilação, permitindo que :::no-loc(Razor)::: os arquivos sejam atualizados se eles forem editados.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-156">When enabled, runtime compilation complements build-time compilation, allowing :::no-loc(Razor)::: files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="b2d1e-157">Compilação de runtime</span><span class="sxs-lookup"><span data-stu-id="b2d1e-157">Runtime compilation</span></span>

<span data-ttu-id="b2d1e-158">Para habilitar a compilação em tempo de execução para todos os ambientes e modos de configuração:</span><span class="sxs-lookup"><span data-stu-id="b2d1e-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="b2d1e-159">Instale o [Microsoft. AspNetCore. Mvc. :::no-loc(Razor)::: . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) Pacote NuGet do RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-159">Install the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="b2d1e-160">Atualize o método do projeto `Startup.ConfigureServices` para incluir uma chamada para <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="b2d1e-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Razor):::RuntimeCompilationMvcBuilderExtensions.Add:::no-loc(Razor):::RuntimeCompilation*>.</span></span> <span data-ttu-id="b2d1e-161">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="b2d1e-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Add:::no-loc(Razor):::Pages()
            .Add:::no-loc(Razor):::RuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="b2d1e-162">Habilitar condicionalmente a compilação em tempo de execução</span><span class="sxs-lookup"><span data-stu-id="b2d1e-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="b2d1e-163">A compilação em tempo de execução pode ser habilitada de modo que esteja disponível apenas para desenvolvimento local.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="b2d1e-164">Habilitar condicionalmente dessa maneira garante que a saída publicada:</span><span class="sxs-lookup"><span data-stu-id="b2d1e-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="b2d1e-165">Usa exibições compiladas.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-165">Uses compiled views.</span></span>
* <span data-ttu-id="b2d1e-166">É menor em tamanho.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-166">Is smaller in size.</span></span>
* <span data-ttu-id="b2d1e-167">Não habilita os inspetores de arquivo na produção.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="b2d1e-168">Para habilitar a compilação em tempo de execução com base no ambiente e no modo de configuração:</span><span class="sxs-lookup"><span data-stu-id="b2d1e-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="b2d1e-169">Referenciar condicionalmente o [Microsoft. AspNetCore. Mvc. :::no-loc(Razor)::: . Pacote RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) com base no `Configuration` valor ativo:</span><span class="sxs-lookup"><span data-stu-id="b2d1e-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="b2d1e-170">Atualize o método do projeto `Startup.ConfigureServices` para incluir uma chamada para `Add:::no-loc(Razor):::RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="b2d1e-170">Update the project's `Startup.ConfigureServices` method to include a call to `Add:::no-loc(Razor):::RuntimeCompilation`.</span></span> <span data-ttu-id="b2d1e-171">Execute condicionalmente `Add:::no-loc(Razor):::RuntimeCompilation` para que ele seja executado somente no modo de depuração quando a `ASPNETCORE_ENVIRONMENT` variável for definida como `Development` :</span><span class="sxs-lookup"><span data-stu-id="b2d1e-171">Conditionally execute `Add:::no-loc(Razor):::RuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="b2d1e-172">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b2d1e-172">Additional resources</span></span>

* <span data-ttu-id="b2d1e-173">Propriedades [ :::no-loc(Razor)::: CompileOnBuild e :::no-loc(Razor)::: CompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="b2d1e-173">[:::no-loc(Razor):::CompileOnBuild and :::no-loc(Razor):::CompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="b2d1e-174">Consulte o [exemplo de compilação em tempo de execução no GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) para obter um exemplo que mostra como fazer trabalhos de compilação de tempo de execução entre projetos.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b2d1e-175">Um :::no-loc(Razor)::: arquivo é compilado em tempo de execução, quando a :::no-loc(Razor)::: página associada ou a exibição MVC é invocada.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-175">A :::no-loc(Razor)::: file is compiled at runtime, when the associated :::no-loc(Razor)::: Page or MVC view is invoked.</span></span> <span data-ttu-id="b2d1e-176">:::no-loc(Razor):::os arquivos são compilados em tempo de compilação e publicação usando o [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="b2d1e-176">:::no-loc(Razor)::: files are compiled at both build and publish time using the [:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="b2d1e-177">:::no-loc(Razor)::: ocorrida</span><span class="sxs-lookup"><span data-stu-id="b2d1e-177">:::no-loc(Razor)::: compilation</span></span>

<span data-ttu-id="b2d1e-178">A compilação de arquivos em tempo de compilação e publicação :::no-loc(Razor)::: é habilitada por padrão pelo :::no-loc(Razor)::: SDK.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-178">Build- and publish-time compilation of :::no-loc(Razor)::: files is enabled by default by the :::no-loc(Razor)::: SDK.</span></span> <span data-ttu-id="b2d1e-179">:::no-loc(Razor):::A edição de arquivos depois que eles são atualizados tem suporte no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-179">Editing :::no-loc(Razor)::: files after they're updated is supported at build time.</span></span> <span data-ttu-id="b2d1e-180">Por padrão, somente os *Views.dll* compilados e nenhum arquivo *. cshtml* ou fazem referência a assemblies necessários para compilar :::no-loc(Razor)::: arquivos são implantados com seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile :::no-loc(Razor)::: files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b2d1e-181">A ferramenta de pré-compilação foi preterida e será removida no ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="b2d1e-182">É recomendável migrar para o [ :::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="b2d1e-182">We recommend migrating to [:::no-loc(Razor)::: Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="b2d1e-183">O :::no-loc(Razor)::: SDK só será eficaz quando nenhuma propriedade específica de pré-compilação for definida no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-183">The :::no-loc(Razor)::: SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="b2d1e-184">Por exemplo, definir a propriedade do arquivo *. csproj* `Mvc:::no-loc(Razor):::CompileOnPublish` para `true` desabilitar o :::no-loc(Razor)::: SDK.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-184">For instance, setting the *.csproj* file's `Mvc:::no-loc(Razor):::CompileOnPublish` property to `true` disables the :::no-loc(Razor)::: SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="b2d1e-185">Compilação de runtime</span><span class="sxs-lookup"><span data-stu-id="b2d1e-185">Runtime compilation</span></span>

<span data-ttu-id="b2d1e-186">A compilação em tempo de compilação é complementada pela compilação de arquivos em tempo de execução :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="b2d1e-186">Build-time compilation is supplemented by runtime compilation of :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="b2d1e-187">ASP.NET Core MVC recompilará :::no-loc(Razor)::: os arquivos quando o conteúdo de um arquivo *. cshtml* for alterado.</span><span class="sxs-lookup"><span data-stu-id="b2d1e-187">ASP.NET Core MVC will recompile :::no-loc(Razor)::: files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b2d1e-188">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b2d1e-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
