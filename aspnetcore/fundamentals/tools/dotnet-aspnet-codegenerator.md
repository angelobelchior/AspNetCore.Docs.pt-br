---
title: comando dotnet aspnet-codegenerator
author: rick-anderson
description: O comando dotnet aspnet-codegenerator aplica scaffold em projetos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 071f2269081e63ad1355547bccb449180c59c997
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016499"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="29ee4-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="29ee4-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="29ee4-104">De [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="29ee4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="29ee4-105">`dotnet aspnet-codegenerator` – executa o mecanismo de scaffolding do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="29ee4-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="29ee4-106">`dotnet aspnet-codegenerator` é necessário somente para fazer scaffold da linha de comando, não é preciso usar o scaffolding com o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="29ee4-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="29ee4-107">Este artigo se aplica ao [SDK do .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) e posteriores.</span><span class="sxs-lookup"><span data-stu-id="29ee4-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="29ee4-108">Instalação do aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="29ee4-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="29ee4-109">`dotnet-aspnet-codegenerator` é uma [ferramenta global](/dotnet/core/tools/global-tools) que deve ser instalada.</span><span class="sxs-lookup"><span data-stu-id="29ee4-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="29ee4-110">O comando a seguir instala a versão estável mais recente da ferramenta `dotnet-aspnet-codegenerator`:</span><span class="sxs-lookup"><span data-stu-id="29ee4-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="29ee4-111">O comando a seguir atualiza `dotnet-aspnet-codegenerator` para a versão estável mais recente disponível dos SDKs do .NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="29ee4-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="29ee4-112">Sinopse</span><span class="sxs-lookup"><span data-stu-id="29ee4-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="29ee4-113">Descrição</span><span class="sxs-lookup"><span data-stu-id="29ee4-113">Description</span></span>

<span data-ttu-id="29ee4-114">O comando global `dotnet aspnet-codegenerator` executa o mecanismo de scaffolding e o gerador de código do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="29ee4-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="29ee4-115">Argumentos</span><span class="sxs-lookup"><span data-stu-id="29ee4-115">Arguments</span></span>

`generator`

<span data-ttu-id="29ee4-116">O gerador de código para ser executado.</span><span class="sxs-lookup"><span data-stu-id="29ee4-116">The code generator to run.</span></span> <span data-ttu-id="29ee4-117">Os geradores a seguir estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="29ee4-117">The following generators are available:</span></span>

| <span data-ttu-id="29ee4-118">Gerador</span><span class="sxs-lookup"><span data-stu-id="29ee4-118">Generator</span></span>  | <span data-ttu-id="29ee4-119">Operação</span><span class="sxs-lookup"><span data-stu-id="29ee4-119">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="29ee4-120">área</span><span class="sxs-lookup"><span data-stu-id="29ee4-120">area</span></span>       | [<span data-ttu-id="29ee4-121">Faz scaffold de uma área</span><span class="sxs-lookup"><span data-stu-id="29ee4-121">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="29ee4-122">controlador</span><span class="sxs-lookup"><span data-stu-id="29ee4-122">controller</span></span> | [<span data-ttu-id="29ee4-123">Faz scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="29ee4-123">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="29ee4-124">identidade</span><span class="sxs-lookup"><span data-stu-id="29ee4-124">identity</span></span>   | [<span data-ttu-id="29ee4-125">Aplica ScaffoldIdentity</span><span class="sxs-lookup"><span data-stu-id="29ee4-125">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="29ee4-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="29ee4-126">razorpage</span></span>  | [<span data-ttu-id="29ee4-127">Páginas do aplica Scaffold Razor</span><span class="sxs-lookup"><span data-stu-id="29ee4-127">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="29ee4-128">exibição</span><span class="sxs-lookup"><span data-stu-id="29ee4-128">view</span></span>       | [<span data-ttu-id="29ee4-129">Faz scaffolds de um modo de exibição</span><span class="sxs-lookup"><span data-stu-id="29ee4-129">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="29ee4-130">Opções</span><span class="sxs-lookup"><span data-stu-id="29ee4-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="29ee4-131">Especifica o diretório de pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="29ee4-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="29ee4-132">Define a configuração da compilação.</span><span class="sxs-lookup"><span data-stu-id="29ee4-132">Defines the build configuration.</span></span> <span data-ttu-id="29ee4-133">O valor padrão é `Debug`.</span><span class="sxs-lookup"><span data-stu-id="29ee4-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="29ee4-134">O [Framework](/dotnet/standard/frameworks) destino para usar.</span><span class="sxs-lookup"><span data-stu-id="29ee4-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="29ee4-135">Por exemplo, `net46`.</span><span class="sxs-lookup"><span data-stu-id="29ee4-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="29ee4-136">O caminho base da compilação.</span><span class="sxs-lookup"><span data-stu-id="29ee4-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="29ee4-137">Imprime uma ajuda breve para o comando.</span><span class="sxs-lookup"><span data-stu-id="29ee4-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="29ee4-138">Não compila o projeto antes da execução.</span><span class="sxs-lookup"><span data-stu-id="29ee4-138">Doesn't build the project before running.</span></span> <span data-ttu-id="29ee4-139">Também define o sinalizador `--no-restore` implicitamente.</span><span class="sxs-lookup"><span data-stu-id="29ee4-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="29ee4-140">Especifica o caminho do arquivo de projeto a ser executado (nome da pasta ou caminho completo).</span><span class="sxs-lookup"><span data-stu-id="29ee4-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="29ee4-141">Se não é especificado, ele usa como padrão o diretório atual.</span><span class="sxs-lookup"><span data-stu-id="29ee4-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="29ee4-142">Opções de gerador</span><span class="sxs-lookup"><span data-stu-id="29ee4-142">Generator options</span></span>

<span data-ttu-id="29ee4-143">As seções a seguir detalham as opções disponíveis para os geradores com suporte:</span><span class="sxs-lookup"><span data-stu-id="29ee4-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="29ee4-144">Área</span><span class="sxs-lookup"><span data-stu-id="29ee4-144">Area</span></span>
* <span data-ttu-id="29ee4-145">Controller</span><span class="sxs-lookup"><span data-stu-id="29ee4-145">Controller</span></span>
* Identity  
* <span data-ttu-id="29ee4-146">Razorpage</span><span class="sxs-lookup"><span data-stu-id="29ee4-146">Razorpage</span></span>
* <span data-ttu-id="29ee4-147">Visualizar</span><span class="sxs-lookup"><span data-stu-id="29ee4-147">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="29ee4-148">Opções de área</span><span class="sxs-lookup"><span data-stu-id="29ee4-148">Area options</span></span>

<span data-ttu-id="29ee4-149">Essa ferramenta destina-se aos projetos Web do ASP.NET Core com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="29ee4-149">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="29ee4-150">Ele não se destina a Razor aplicativos de páginas.</span><span class="sxs-lookup"><span data-stu-id="29ee4-150">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="29ee4-151">Uso: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="29ee4-151">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="29ee4-152">O comando anterior gera as seguintes pastas:</span><span class="sxs-lookup"><span data-stu-id="29ee4-152">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="29ee4-153">*Áreas*</span><span class="sxs-lookup"><span data-stu-id="29ee4-153">*Areas*</span></span>
  * <span data-ttu-id="29ee4-154">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="29ee4-154">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="29ee4-155">*Controladores*</span><span class="sxs-lookup"><span data-stu-id="29ee4-155">*Controllers*</span></span>
    * <span data-ttu-id="29ee4-156">*Dados*</span><span class="sxs-lookup"><span data-stu-id="29ee4-156">*Data*</span></span>
    * <span data-ttu-id="29ee4-157">*Modelos*</span><span class="sxs-lookup"><span data-stu-id="29ee4-157">*Models*</span></span>
    * <span data-ttu-id="29ee4-158">*Exibições*</span><span class="sxs-lookup"><span data-stu-id="29ee4-158">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="29ee4-159">Opções de controlador</span><span class="sxs-lookup"><span data-stu-id="29ee4-159">Controller options</span></span>

<span data-ttu-id="29ee4-160">A tabela a seguir lista as opções para o `aspnet-codegenerator` `controller` e o `razorpage` :</span><span class="sxs-lookup"><span data-stu-id="29ee4-160">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="29ee4-161">A tabela a seguir lista as opções exclusivas para `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="29ee4-161">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="29ee4-162">Opção</span><span class="sxs-lookup"><span data-stu-id="29ee4-162">Option</span></span>                         | <span data-ttu-id="29ee4-163">Descrição</span><span class="sxs-lookup"><span data-stu-id="29ee4-163">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="29ee4-164">--controllerName ou -name</span><span class="sxs-lookup"><span data-stu-id="29ee4-164">--controllerName or -name</span></span>      | <span data-ttu-id="29ee4-165">O nome do controlador.</span><span class="sxs-lookup"><span data-stu-id="29ee4-165">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="29ee4-166">--useAsyncActions ou -async</span><span class="sxs-lookup"><span data-stu-id="29ee4-166">--useAsyncActions or -async</span></span>    | <span data-ttu-id="29ee4-167">Gera ações do controlador assíncrono.</span><span class="sxs-lookup"><span data-stu-id="29ee4-167">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="29ee4-168">--noViews ou -nv</span><span class="sxs-lookup"><span data-stu-id="29ee4-168">--noViews or -nv</span></span>               | <span data-ttu-id="29ee4-169">**Não** gera modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="29ee4-169">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="29ee4-170">--restWithNoViews ou -api</span><span class="sxs-lookup"><span data-stu-id="29ee4-170">--restWithNoViews or -api</span></span>      | <span data-ttu-id="29ee4-171">Gera um controlador com API estilo REST.</span><span class="sxs-lookup"><span data-stu-id="29ee4-171">Generate a Controller with REST style API.</span></span> <span data-ttu-id="29ee4-172">É assumido `noViews` e todas as opções relacionadas à visualização são ignoradas.</span><span class="sxs-lookup"><span data-stu-id="29ee4-172">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="29ee4-173">--readWriteActions ou -actions</span><span class="sxs-lookup"><span data-stu-id="29ee4-173">--readWriteActions or -actions</span></span> | <span data-ttu-id="29ee4-174">Gere o controlador com ações de leitura/gravação sem um modelo.</span><span class="sxs-lookup"><span data-stu-id="29ee4-174">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="29ee4-175">Use o switch `-h` para obter ajuda sobre o comando `aspnet-codegenerator controller`:</span><span class="sxs-lookup"><span data-stu-id="29ee4-175">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="29ee4-176">Confira [Fazer scaffold do modelo de filme](xref:tutorials/first-mvc-app/adding-model) para obter um exemplo de `dotnet aspnet-codegenerator controller`.</span><span class="sxs-lookup"><span data-stu-id="29ee4-176">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="29ee4-177">Razorpage</span><span class="sxs-lookup"><span data-stu-id="29ee4-177">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="29ee4-178">RazorAs páginas podem ser com Scaffold individualmente especificando o nome da nova página e o modelo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="29ee4-178">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="29ee4-179">Os modelos com suporte são:</span><span class="sxs-lookup"><span data-stu-id="29ee4-179">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="29ee4-180">Por exemplo, o comando a seguir usa o modelo Editar para gerar *MyEdit.cshtml* e *MyEdit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="29ee4-180">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="29ee4-181">Normalmente, o modelo e o nome de arquivo gerado não são especificados e os seguintes modelos são criados:</span><span class="sxs-lookup"><span data-stu-id="29ee4-181">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="29ee4-182">A tabela a seguir lista as opções para o `aspnet-codegenerator` `razorpage` e o `controller` :</span><span class="sxs-lookup"><span data-stu-id="29ee4-182">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="29ee4-183">A tabela a seguir lista as opções exclusivas para `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="29ee4-183">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="29ee4-184">Opção</span><span class="sxs-lookup"><span data-stu-id="29ee4-184">Option</span></span>                        | <span data-ttu-id="29ee4-185">Descrição</span><span class="sxs-lookup"><span data-stu-id="29ee4-185">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="29ee4-186">--namespaceName ou -namespace</span><span class="sxs-lookup"><span data-stu-id="29ee4-186">--namespaceName or -namespace</span></span> | <span data-ttu-id="29ee4-187">O nome do namespace a ser usado no PageModel gerado</span><span class="sxs-lookup"><span data-stu-id="29ee4-187">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="29ee4-188">--partialView ou -partial</span><span class="sxs-lookup"><span data-stu-id="29ee4-188">--partialView or -partial</span></span>     | <span data-ttu-id="29ee4-189">Gere uma exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="29ee4-189">Generate a partial view.</span></span> <span data-ttu-id="29ee4-190">As opções de layout -l e - udl são ignoradas, se isso for especificado.</span><span class="sxs-lookup"><span data-stu-id="29ee4-190">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="29ee4-191">--noPageModel ou -npm</span><span class="sxs-lookup"><span data-stu-id="29ee4-191">--noPageModel or -npm</span></span>         | <span data-ttu-id="29ee4-192">Alterne para não gerar uma classe PageModel para o modelo Vazio</span><span class="sxs-lookup"><span data-stu-id="29ee4-192">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="29ee4-193">Use o switch `-h` para obter ajuda sobre o comando `aspnet-codegenerator razorpage`:</span><span class="sxs-lookup"><span data-stu-id="29ee4-193">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="29ee4-194">Confira [Fazer scaffold do modelo de filme](xref:tutorials/razor-pages/model) para obter um exemplo de `dotnet aspnet-codegenerator razorpage`.</span><span class="sxs-lookup"><span data-stu-id="29ee4-194">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="29ee4-195">Consulte [Scaffold Identity ](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="29ee4-195">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
