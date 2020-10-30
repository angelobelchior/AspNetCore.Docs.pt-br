---
title: Criar um cliente e um servidor gRPC do .NET Core no ASP.NET Core
author: juntaoluo
description: Este tutorial mostra como criar um serviço gRPC e um cliente gRPC no ASP.NET Core. Saiba como criar um projeto de serviço gRPC, editar um arquivo pronto e adicionar uma chamada de streaming duplex.
ms.author: johluo
ms.date: 10/23/2020
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 9388a2f814008ebb50171f85b8baccf6dadfac27
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057018"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="12e5e-104">Tutorial: criar um cliente gRPC e um servidor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="12e5e-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="12e5e-105">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="12e5e-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="12e5e-106">Este tutorial mostra como criar um cliente [gRPC](https://grpc.io/docs/guides/) do .NET Core e um servidor gRPC do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="12e5e-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="12e5e-107">No final, você terá um cliente gRPC que se comunica com o serviço de Boas-vindas do gRPC.</span><span class="sxs-lookup"><span data-stu-id="12e5e-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="12e5e-108">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="12e5e-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="12e5e-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="12e5e-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="12e5e-110">Criará um servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="12e5e-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="12e5e-111">Criará um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="12e5e-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="12e5e-112">Testará o serviço do cliente gRPC com o serviço Greeter do gRPC.</span><span class="sxs-lookup"><span data-stu-id="12e5e-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="12e5e-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="12e5e-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="12e5e-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12e5e-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="12e5e-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12e5e-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12e5e-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="12e5e-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="12e5e-117">Visual Studio para Mac versão 8,7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="12e5e-117">Visual Studio for Mac version 8.7 or later</span></span>](/visualstudio/releasenotes/vs2019-mac-relnotes)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]
---

## <a name="create-a-grpc-service"></a><span data-ttu-id="12e5e-118">Criar um serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="12e5e-118">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="12e5e-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12e5e-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="12e5e-120">Inicie o Visual Studio e selecione **Criar um projeto** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-120">Start Visual Studio and select **Create a new project** .</span></span> <span data-ttu-id="12e5e-121">Como alternativa, no menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-121">Alternatively, from the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="12e5e-122">Na caixa de diálogo **criar um novo projeto** , selecione **serviço GRPC** e selecione **Avançar** :</span><span class="sxs-lookup"><span data-stu-id="12e5e-122">In the **Create a new project** dialog, select **gRPC Service** and select **Next** :</span></span>

  ![Criar uma caixa de diálogo de novo projeto no Visual Studio](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="12e5e-124">Nomeie o projeto **GrpcGreeter** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-124">Name the project **GrpcGreeter** .</span></span> <span data-ttu-id="12e5e-125">É importante nomear o projeto *GrpcGreeter* para que os namespaces correspondam quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="12e5e-125">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="12e5e-126">Selecione **Criar** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-126">Select **Create** .</span></span>
* <span data-ttu-id="12e5e-127">Na caixa de diálogo **Criar um novo serviço gRPC** :</span><span class="sxs-lookup"><span data-stu-id="12e5e-127">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="12e5e-128">O modelo de **Serviço gRPC** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="12e5e-128">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="12e5e-129">Selecione **Criar** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-129">Select **Create** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="12e5e-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12e5e-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="12e5e-131">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="12e5e-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="12e5e-132">Altere os diretórios ( `cd` ) para uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="12e5e-132">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="12e5e-133">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="12e5e-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="12e5e-134">O comando `dotnet new` cria um novo serviço gRPC na pasta *GrpcGreeter* .</span><span class="sxs-lookup"><span data-stu-id="12e5e-134">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="12e5e-135">O comando `code` abre a pasta *GrpcGreeter* em uma nova instância do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="12e5e-135">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="12e5e-136">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' GrpcGreeter '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="12e5e-136">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="12e5e-137">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="12e5e-137">Select **Yes** .</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12e5e-138">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="12e5e-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="12e5e-139">Inicie o Visual Studio para Mac e selecione **criar um novo projeto** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-139">Start Visual Studio for Mac and select **Create a new project** .</span></span> <span data-ttu-id="12e5e-140">Como alternativa, no menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-140">Alternatively, from the Visual Studio **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="12e5e-141">Na caixa de diálogo **criar um novo projeto** , selecione **Web e console**  >  **aplicativo**  >  **gRPC serviço** e selecione **Avançar** :</span><span class="sxs-lookup"><span data-stu-id="12e5e-141">In the **Create a new project** dialog, select **Web and Console** > **App** > **gRPC Service** and select **Next** :</span></span>

  ![Criar uma caixa de diálogo novo projeto no macOS](~/tutorials/grpc/grpc-start/static/cnp-mac.png)

* <span data-ttu-id="12e5e-143">Selecione **.NET Core 3,1** para a estrutura de destino e selecione **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-143">Select **.NET Core 3.1** for the target framework and select **Next** .</span></span>
* <span data-ttu-id="12e5e-144">Nomeie o projeto **GrpcGreeter** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-144">Name the project **GrpcGreeter** .</span></span> <span data-ttu-id="12e5e-145">É importante nomear o projeto *GrpcGreeter* para que os namespaces correspondam quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="12e5e-145">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="12e5e-146">Selecione **Criar** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-146">Select **Create** .</span></span>
---

### <a name="run-the-service"></a><span data-ttu-id="12e5e-147">Executar o serviço</span><span class="sxs-lookup"><span data-stu-id="12e5e-147">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="12e5e-148">Os logs mostram o serviço escutando em `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="12e5e-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="12e5e-149">O modelo gRPC é configurado para usar [o protocolo TLS](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="12e5e-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="12e5e-150">Os clientes gRPC precisam usar HTTPS para chamar o servidor.</span><span class="sxs-lookup"><span data-stu-id="12e5e-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="12e5e-151">O macOS não é compatível com gRPC do ASP.NET Core com TLS.</span><span class="sxs-lookup"><span data-stu-id="12e5e-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="12e5e-152">É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS.</span><span class="sxs-lookup"><span data-stu-id="12e5e-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="12e5e-153">Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="12e5e-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="12e5e-154">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="12e5e-154">Examine the project files</span></span>

<span data-ttu-id="12e5e-155">Arquivos de projeto *GrpcGreeter* :</span><span class="sxs-lookup"><span data-stu-id="12e5e-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="12e5e-156">*Greet. proto* : o arquivo *Protos/Greet. proto* define o `Greeter` gRPC e é usado para gerar os ativos do servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="12e5e-156">*greet.proto* : The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="12e5e-157">Para obter mais informações, confira [Introdução ao gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="12e5e-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="12e5e-158">Pasta de *Serviços* : contém a implementação do `Greeter` serviço.</span><span class="sxs-lookup"><span data-stu-id="12e5e-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="12e5e-159">*appSettings.jsem* : contém dados de configuração, como o protocolo usado pelo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="12e5e-159">*appSettings.json* : Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="12e5e-160">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="12e5e-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="12e5e-161">*Program.cs* : contém o ponto de entrada para o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="12e5e-161">*Program.cs* : Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="12e5e-162">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="12e5e-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="12e5e-163">*Startup.cs* : contém o código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="12e5e-163">*Startup.cs* : Contains code that configures app behavior.</span></span> <span data-ttu-id="12e5e-164">Para obter mais informações, veja [Inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="12e5e-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="12e5e-165">Criar o cliente gRPC em um aplicativo de console .NET</span><span class="sxs-lookup"><span data-stu-id="12e5e-165">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="12e5e-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12e5e-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="12e5e-167">Abra uma segunda instância do Visual Studio e selecione **Criar um novo projeto** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-167">Open a second instance of Visual Studio and select **Create a new project** .</span></span>
* <span data-ttu-id="12e5e-168">Na caixa de diálogo **Criar um novo projeto** , selecione **Aplicativo de Console (.NET Core)** e **Avançar** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-168">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next** .</span></span>
* <span data-ttu-id="12e5e-169">Na caixa de texto **nome do projeto** , digite **GrpcGreeterClient** e selecione **criar** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-169">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="12e5e-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12e5e-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="12e5e-171">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="12e5e-171">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="12e5e-172">Altere os diretórios ( `cd` ) para uma pasta para o projeto.</span><span class="sxs-lookup"><span data-stu-id="12e5e-172">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="12e5e-173">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="12e5e-173">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12e5e-174">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="12e5e-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="12e5e-175">Siga as instruções em [Compilar uma solução completa do .NET Core no macOS usando o Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) para criar um aplicativo de console com o nome *GrpcGreeterClient* .</span><span class="sxs-lookup"><span data-stu-id="12e5e-175">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient* .</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="12e5e-176">Adicionar os pacotes necessários</span><span class="sxs-lookup"><span data-stu-id="12e5e-176">Add required packages</span></span>

<span data-ttu-id="12e5e-177">O projeto cliente gRPC requer os seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="12e5e-177">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="12e5e-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), que contém o cliente do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="12e5e-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="12e5e-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contém APIs de mensagem protobuf para C#.</span><span class="sxs-lookup"><span data-stu-id="12e5e-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="12e5e-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contém o suporte a ferramentas C# para arquivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="12e5e-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="12e5e-181">O pacote de ferramentas não é necessário em runtime e, portanto, a dependência é marcada com `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="12e5e-181">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="12e5e-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12e5e-182">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="12e5e-183">Instalar os pacotes usando o PMC (Console do Gerenciador de Pacotes) ou Gerenciar Pacotes NuGet.</span><span class="sxs-lookup"><span data-stu-id="12e5e-183">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="12e5e-184">Opção do PMC para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="12e5e-184">PMC option to install packages</span></span>

* <span data-ttu-id="12e5e-185">No Visual Studio, selecione **ferramentas**  >  **Gerenciador de pacotes NuGet**  >  **console do Gerenciador de pacotes**</span><span class="sxs-lookup"><span data-stu-id="12e5e-185">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="12e5e-186">Na janela do **Console do Gerenciador de Pacotes** , execute `cd GrpcGreeterClient` para alterar os diretórios para a pasta que contém os arquivos *GrpcGreeterClient.csproj* .</span><span class="sxs-lookup"><span data-stu-id="12e5e-186">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="12e5e-187">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="12e5e-187">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="12e5e-188">Opção Gerenciar Pacotes NuGet para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="12e5e-188">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="12e5e-189">Clique com o botão direito do mouse no projeto em **Gerenciador de soluções**  >  **gerenciar pacotes NuGet** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-189">Right-click the project in **Solution Explorer** > **Manage NuGet Packages** .</span></span>
* <span data-ttu-id="12e5e-190">Selecione a guia **Procurar** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-190">Select the **Browse** tab.</span></span>
* <span data-ttu-id="12e5e-191">Insira **Grpc.Net.Client** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="12e5e-191">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="12e5e-192">Selecione o pacote **Grpc.Net.Client** na guia **Procurar** e selecione **Instalar** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-192">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install** .</span></span>
* <span data-ttu-id="12e5e-193">Repita para `Google.Protobuf` e `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="12e5e-193">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="12e5e-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12e5e-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="12e5e-195">Execute os comandos a seguir no **Terminal Integrado** :</span><span class="sxs-lookup"><span data-stu-id="12e5e-195">Run the following commands from the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12e5e-196">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="12e5e-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="12e5e-197">Clique com o botão direito do mouse em projeto **GrpcGreeterClient** na **painel de soluções** e selecione **gerenciar pacotes NuGet** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-197">Right-click **GrpcGreeterClient** project in the **Solution Pad** and select **Manage NuGet Packages** .</span></span>
* <span data-ttu-id="12e5e-198">Insira **Grpc.Net.Client** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="12e5e-198">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="12e5e-199">Selecione o pacote **Grpc .net. Client** no painel de resultados e selecione **Adicionar pacote** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-199">Select the **Grpc.Net.Client** package from the results pane and select **Add Package** .</span></span>
* <span data-ttu-id="12e5e-200">Selecione o botão **aceitar** na caixa de diálogo **aceitar licença** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-200">Select the **Accept** button on the **Accept License** dialog.</span></span>
* <span data-ttu-id="12e5e-201">Repita para `Google.Protobuf` e `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="12e5e-201">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="12e5e-202">Adicionar greet.proto</span><span class="sxs-lookup"><span data-stu-id="12e5e-202">Add greet.proto</span></span>

* <span data-ttu-id="12e5e-203">Crie uma pasta *Protos* no projeto do cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="12e5e-203">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="12e5e-204">Copie o arquivo *Protos\greet.proto* do serviço de Boas-vindas do gRPC para o projeto de cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="12e5e-204">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="12e5e-205">Atualize o namespace dentro do `greet.proto` arquivo para o namespace do projeto:</span><span class="sxs-lookup"><span data-stu-id="12e5e-205">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="12e5e-206">Edite o arquivo de projeto *GrpcGreeterClient.csproj* :</span><span class="sxs-lookup"><span data-stu-id="12e5e-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="12e5e-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12e5e-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="12e5e-208">Clique com o botão direito do mouse no projeto e selecione **Editar Arquivo de Projeto** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-208">Right-click the project and select **Edit Project File** .</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="12e5e-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12e5e-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="12e5e-210">Selecione o arquivo *GrpcGreeterClient.csproj* .</span><span class="sxs-lookup"><span data-stu-id="12e5e-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12e5e-211">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="12e5e-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="12e5e-212">Clique com o botão direito do mouse no projeto e selecione **Editar Arquivo de Projeto** .</span><span class="sxs-lookup"><span data-stu-id="12e5e-212">Right-click the project and select **Edit Project File** .</span></span>

  ---

* <span data-ttu-id="12e5e-213">Adicione um grupo de itens com um elemento `<Protobuf>` que faça referência ao arquivo *greet.proto* :</span><span class="sxs-lookup"><span data-stu-id="12e5e-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="12e5e-214">Criar o cliente Greeter</span><span class="sxs-lookup"><span data-stu-id="12e5e-214">Create the Greeter client</span></span>

<span data-ttu-id="12e5e-215">Crie o projeto do cliente para criar os tipos no `GrpcGreeter` namespace.</span><span class="sxs-lookup"><span data-stu-id="12e5e-215">Build the client project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="12e5e-216">Os tipos `GrpcGreeter` são gerados automaticamente pelo processo de build.</span><span class="sxs-lookup"><span data-stu-id="12e5e-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="12e5e-217">Atualize o arquivo *Program.cs* do cliente gRPC com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="12e5e-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="12e5e-218">*Program.cs* contém o ponto de entrada e lógica para o cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="12e5e-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="12e5e-219">O cliente Greeter é criado da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="12e5e-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="12e5e-220">Criando uma instância de `GrpcChannel` que contém as informações para criar a conexão com o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="12e5e-220">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="12e5e-221">Como usar o `GrpcChannel` para construir o cliente Greeter:</span><span class="sxs-lookup"><span data-stu-id="12e5e-221">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="12e5e-222">O cliente Greeter chama o método `SayHello` assíncrono.</span><span class="sxs-lookup"><span data-stu-id="12e5e-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="12e5e-223">O resultado da chamada `SayHello` é exibido:</span><span class="sxs-lookup"><span data-stu-id="12e5e-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="12e5e-224">Testar o cliente gRPC com o serviço de Boas-vindas do gRPC</span><span class="sxs-lookup"><span data-stu-id="12e5e-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="12e5e-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12e5e-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="12e5e-226">No serviço Greeter, pressione `Ctrl+F5` para iniciar o servidor sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="12e5e-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="12e5e-227">No projeto `GrpcGreeterClient`, pressione `Ctrl+F5` para iniciar o cliente sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="12e5e-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="12e5e-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12e5e-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="12e5e-229">Inicie o serviço Greeter.</span><span class="sxs-lookup"><span data-stu-id="12e5e-229">Start the Greeter service.</span></span>
* <span data-ttu-id="12e5e-230">Inicie o cliente.</span><span class="sxs-lookup"><span data-stu-id="12e5e-230">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="12e5e-231">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="12e5e-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="12e5e-232">Devido ao [problema do protocolo http/2 TLS mencionado anteriormente na solução alternativa do MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos), você precisará atualizar o endereço do canal no cliente para " http://localhost:5000 ".</span><span class="sxs-lookup"><span data-stu-id="12e5e-232">Due to the previously mentioned [HTTP/2 TLS issue on macOS workaround](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos), you'll need to update the channel address in the client to "http://localhost:5000".</span></span> <span data-ttu-id="12e5e-233">Atualize a linha 13 de **GrpcGreeterClient/Program. cs** para ler:</span><span class="sxs-lookup"><span data-stu-id="12e5e-233">Update line 13 of **GrpcGreeterClient/Program.cs** to read:</span></span>
  ```csharp
  using var channel = GrpcChannel.ForAddress("http://localhost:5000");
  ``` 
* <span data-ttu-id="12e5e-234">Inicie o serviço Greeter.</span><span class="sxs-lookup"><span data-stu-id="12e5e-234">Start the Greeter service.</span></span>
* <span data-ttu-id="12e5e-235">Inicie o cliente.</span><span class="sxs-lookup"><span data-stu-id="12e5e-235">Start the client.</span></span>

---

<span data-ttu-id="12e5e-236">O cliente envia uma saudação para o serviço com uma mensagem contendo seu nome, *GreeterClient* .</span><span class="sxs-lookup"><span data-stu-id="12e5e-236">The client sends a greeting to the service with a message containing its name, *GreeterClient* .</span></span> <span data-ttu-id="12e5e-237">O serviço envia a mensagem "Olá, GreeterClient" como uma resposta.</span><span class="sxs-lookup"><span data-stu-id="12e5e-237">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="12e5e-238">A resposta "Olá, GreeterClient" é exibida no prompt de comando:</span><span class="sxs-lookup"><span data-stu-id="12e5e-238">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="12e5e-239">O serviço gRPC registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando:</span><span class="sxs-lookup"><span data-stu-id="12e5e-239">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> <span data-ttu-id="12e5e-240">O código neste artigo requer o certificado de desenvolvimento HTTPS do ASP.NET Core para proteger o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="12e5e-240">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="12e5e-241">Se o cliente .NET gRPC falhar com a mensagem `The remote certificate is invalid according to the validation procedure.` ou `The SSL connection could not be established.` , o certificado de desenvolvimento não será confiável.</span><span class="sxs-lookup"><span data-stu-id="12e5e-241">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="12e5e-242">Para corrigir esse problema, consulte [chamar um serviço gRPC com um certificado não confiável/inválido](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span><span class="sxs-lookup"><span data-stu-id="12e5e-242">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="12e5e-243">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="12e5e-243">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
