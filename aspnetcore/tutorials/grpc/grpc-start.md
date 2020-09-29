---
title: Criar um cliente e um servidor gRPC do .NET Core no ASP.NET Core
author: juntaoluo
description: Este tutorial mostra como criar um serviço gRPC e um cliente gRPC no ASP.NET Core. Saiba como criar um projeto de serviço gRPC, editar um arquivo pronto e adicionar uma chamada de streaming duplex.
ms.author: johluo
ms.date: 04/08/2020
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 13eb57bbe671dcc70a1678222a98590f4edc6e6f
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424250"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="6f76b-104">Tutorial: criar um cliente gRPC e um servidor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6f76b-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="6f76b-105">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="6f76b-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="6f76b-106">Este tutorial mostra como criar um cliente [gRPC](https://grpc.io/docs/guides/) do .NET Core e um servidor gRPC do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f76b-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="6f76b-107">No final, você terá um cliente gRPC que se comunica com o serviço de Boas-vindas do gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f76b-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="6f76b-108">[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6f76b-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="6f76b-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="6f76b-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6f76b-110">Criará um servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f76b-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="6f76b-111">Criará um cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f76b-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="6f76b-112">Testará o serviço do cliente gRPC com o serviço Greeter do gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f76b-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6f76b-113">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="6f76b-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6f76b-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f76b-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6f76b-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f76b-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6f76b-116">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6f76b-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="6f76b-117">Criar um serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="6f76b-117">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6f76b-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f76b-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6f76b-119">Inicie o Visual Studio e selecione **Criar um projeto**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="6f76b-120">Como alternativa, no menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="6f76b-121">Na caixa de diálogo **criar um novo projeto** , selecione **serviço GRPC** e selecione **Avançar**:</span><span class="sxs-lookup"><span data-stu-id="6f76b-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![Caixa de diálogo criar um novo projeto](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="6f76b-123">Nomeie o projeto **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="6f76b-124">É importante nomear o projeto *GrpcGreeter* de modo que os namespaces façam a correspondência quando você copiar e colar o código.</span><span class="sxs-lookup"><span data-stu-id="6f76b-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="6f76b-125">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-125">Select **Create**.</span></span>
* <span data-ttu-id="6f76b-126">Na caixa de diálogo **Criar um novo serviço gRPC**:</span><span class="sxs-lookup"><span data-stu-id="6f76b-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="6f76b-127">O modelo de **Serviço gRPC** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="6f76b-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="6f76b-128">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-128">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6f76b-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f76b-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6f76b-130">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6f76b-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="6f76b-131">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="6f76b-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="6f76b-132">Execute os comandos a seguir:</span><span class="sxs-lookup"><span data-stu-id="6f76b-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="6f76b-133">O comando `dotnet new` cria um novo serviço gRPC na pasta *GrpcGreeter*.</span><span class="sxs-lookup"><span data-stu-id="6f76b-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="6f76b-134">O comando `code` abre a pasta *GrpcGreeter* em uma nova instância do Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6f76b-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="6f76b-135">Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' GrpcGreeter '. Adicioná-los?**</span><span class="sxs-lookup"><span data-stu-id="6f76b-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="6f76b-136">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="6f76b-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6f76b-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6f76b-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6f76b-138">Em um terminal, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6f76b-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="6f76b-139">Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f76b-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="6f76b-140">Abrir o projeto</span><span class="sxs-lookup"><span data-stu-id="6f76b-140">Open the project</span></span>

<span data-ttu-id="6f76b-141">No Visual Studio, selecione **arquivo**  >  **abrir**e, em seguida, selecione o arquivo *GrpcGreeter. csproj* .</span><span class="sxs-lookup"><span data-stu-id="6f76b-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.csproj* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="6f76b-142">Executar o serviço</span><span class="sxs-lookup"><span data-stu-id="6f76b-142">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="6f76b-143">Os logs mostram o serviço escutando em `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="6f76b-143">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="6f76b-144">O modelo gRPC é configurado para usar [o protocolo TLS](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="6f76b-144">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="6f76b-145">Os clientes gRPC precisam usar HTTPS para chamar o servidor.</span><span class="sxs-lookup"><span data-stu-id="6f76b-145">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="6f76b-146">O macOS não é compatível com gRPC do ASP.NET Core com TLS.</span><span class="sxs-lookup"><span data-stu-id="6f76b-146">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="6f76b-147">É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS.</span><span class="sxs-lookup"><span data-stu-id="6f76b-147">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="6f76b-148">Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="6f76b-148">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="6f76b-149">Examinar os arquivos de projeto</span><span class="sxs-lookup"><span data-stu-id="6f76b-149">Examine the project files</span></span>

<span data-ttu-id="6f76b-150">Arquivos de projeto *GrpcGreeter*:</span><span class="sxs-lookup"><span data-stu-id="6f76b-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="6f76b-151">*Greet. proto*: o arquivo *Protos/Greet. proto* define o `Greeter` gRPC e é usado para gerar os ativos do servidor gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f76b-151">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="6f76b-152">Para obter mais informações, confira [Introdução ao gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="6f76b-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="6f76b-153">Pasta de *Serviços* : contém a implementação do `Greeter` serviço.</span><span class="sxs-lookup"><span data-stu-id="6f76b-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="6f76b-154">*appSettings.jsem*: contém dados de configuração, como o protocolo usado pelo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6f76b-154">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="6f76b-155">Para obter mais informações, consulte <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="6f76b-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="6f76b-156">*Program.cs*: contém o ponto de entrada para o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f76b-156">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="6f76b-157">Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="6f76b-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="6f76b-158">*Startup.cs*: contém o código que configura o comportamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6f76b-158">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="6f76b-159">Para obter mais informações, veja [Inicialização do aplicativo](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="6f76b-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="6f76b-160">Criar o cliente gRPC em um aplicativo de console .NET</span><span class="sxs-lookup"><span data-stu-id="6f76b-160">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6f76b-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f76b-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6f76b-162">Abra uma segunda instância do Visual Studio e selecione **Criar um novo projeto**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-162">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="6f76b-163">Na caixa de diálogo **Criar um novo projeto**, selecione **Aplicativo de Console (.NET Core)** e **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-163">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="6f76b-164">Na caixa de texto **nome do projeto** , digite **GrpcGreeterClient** e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-164">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6f76b-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f76b-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6f76b-166">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6f76b-166">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="6f76b-167">Altere os diretórios (`cd`) para uma pasta que conterá o projeto.</span><span class="sxs-lookup"><span data-stu-id="6f76b-167">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="6f76b-168">Execute os comandos a seguir:</span><span class="sxs-lookup"><span data-stu-id="6f76b-168">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6f76b-169">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6f76b-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6f76b-170">Siga as instruções em [Compilar uma solução completa do .NET Core no macOS usando o Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) para criar um aplicativo de console com o nome *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="6f76b-170">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="6f76b-171">Adicionar os pacotes necessários</span><span class="sxs-lookup"><span data-stu-id="6f76b-171">Add required packages</span></span>

<span data-ttu-id="6f76b-172">O projeto cliente gRPC requer os seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="6f76b-172">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="6f76b-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), que contém o cliente do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f76b-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="6f76b-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contém APIs de mensagem protobuf para C#.</span><span class="sxs-lookup"><span data-stu-id="6f76b-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="6f76b-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contém o suporte a ferramentas C# para arquivos protobuf.</span><span class="sxs-lookup"><span data-stu-id="6f76b-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="6f76b-176">O pacote de ferramentas não é necessário em runtime e, portanto, a dependência é marcada com `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="6f76b-176">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6f76b-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f76b-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6f76b-178">Instalar os pacotes usando o PMC (Console do Gerenciador de Pacotes) ou Gerenciar Pacotes NuGet.</span><span class="sxs-lookup"><span data-stu-id="6f76b-178">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="6f76b-179">Opção do PMC para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="6f76b-179">PMC option to install packages</span></span>

* <span data-ttu-id="6f76b-180">No Visual Studio, selecione **ferramentas**  >  **Gerenciador de pacotes NuGet**  >  **console do Gerenciador de pacotes**</span><span class="sxs-lookup"><span data-stu-id="6f76b-180">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="6f76b-181">Na janela do **Console do Gerenciador de Pacotes**, execute `cd GrpcGreeterClient` para alterar os diretórios para a pasta que contém os arquivos *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="6f76b-181">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="6f76b-182">Execute os comandos a seguir:</span><span class="sxs-lookup"><span data-stu-id="6f76b-182">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="6f76b-183">Opção Gerenciar Pacotes NuGet para instalar pacotes</span><span class="sxs-lookup"><span data-stu-id="6f76b-183">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="6f76b-184">Clique com o botão direito do mouse no projeto no **Gerenciador de soluções**  >  **gerenciar pacotes NuGet**</span><span class="sxs-lookup"><span data-stu-id="6f76b-184">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="6f76b-185">Selecione a guia **Procurar**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-185">Select the **Browse** tab.</span></span>
* <span data-ttu-id="6f76b-186">Insira **Grpc.Net.Client** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="6f76b-186">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="6f76b-187">Selecione o pacote **Grpc.Net.Client** na guia **Procurar** e selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-187">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="6f76b-188">Repita para `Google.Protobuf` e `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="6f76b-188">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6f76b-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f76b-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6f76b-190">Execute os comandos a seguir no **Terminal Integrado**:</span><span class="sxs-lookup"><span data-stu-id="6f76b-190">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6f76b-191">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6f76b-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6f76b-192">Clique com o botão direito do mouse na pasta **Pacotes** em **Painel de Soluções** > **Adicionar Pacotes**</span><span class="sxs-lookup"><span data-stu-id="6f76b-192">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="6f76b-193">Insira **Grpc.Net.Client** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="6f76b-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="6f76b-194">Selecione o pacote **Grpc.Net.Client** no painel de resultados e selecione **Adicionar Pacote**</span><span class="sxs-lookup"><span data-stu-id="6f76b-194">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="6f76b-195">Repita para `Google.Protobuf` e `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="6f76b-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="6f76b-196">Adicionar greet.proto</span><span class="sxs-lookup"><span data-stu-id="6f76b-196">Add greet.proto</span></span>

* <span data-ttu-id="6f76b-197">Crie uma pasta *Protos* no projeto do cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f76b-197">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="6f76b-198">Copie o arquivo *Protos\greet.proto* do serviço de Boas-vindas do gRPC para o projeto de cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f76b-198">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="6f76b-199">Atualize o namespace dentro do `greet.proto` arquivo para o namespace do projeto:</span><span class="sxs-lookup"><span data-stu-id="6f76b-199">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="6f76b-200">Edite o arquivo de projeto *GrpcGreeterClient.csproj*:</span><span class="sxs-lookup"><span data-stu-id="6f76b-200">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="6f76b-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f76b-201">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="6f76b-202">Clique com o botão direito do mouse no projeto e selecione **Editar Arquivo de Projeto**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-202">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="6f76b-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f76b-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="6f76b-204">Selecione o arquivo *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="6f76b-204">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6f76b-205">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6f76b-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="6f76b-206">Clique com o botão direito do mouse no projeto e selecione **ferramentas**  >  **Editar arquivo**.</span><span class="sxs-lookup"><span data-stu-id="6f76b-206">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="6f76b-207">Adicione um grupo de itens com um elemento `<Protobuf>` que faça referência ao arquivo *greet.proto*:</span><span class="sxs-lookup"><span data-stu-id="6f76b-207">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="6f76b-208">Criar o cliente Greeter</span><span class="sxs-lookup"><span data-stu-id="6f76b-208">Create the Greeter client</span></span>

<span data-ttu-id="6f76b-209">Compile o projeto para criar os tipos no namespace `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="6f76b-209">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="6f76b-210">Os tipos `GrpcGreeter` são gerados automaticamente pelo processo de build.</span><span class="sxs-lookup"><span data-stu-id="6f76b-210">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="6f76b-211">Atualize o arquivo *Program.cs* do cliente gRPC com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6f76b-211">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="6f76b-212">*Program.cs* contém o ponto de entrada e lógica para o cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f76b-212">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="6f76b-213">O cliente Greeter é criado da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="6f76b-213">The Greeter client is created by:</span></span>

* <span data-ttu-id="6f76b-214">Criando uma instância de `GrpcChannel` que contém as informações para criar a conexão com o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f76b-214">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="6f76b-215">Como usar o `GrpcChannel` para construir o cliente Greeter:</span><span class="sxs-lookup"><span data-stu-id="6f76b-215">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="6f76b-216">O cliente Greeter chama o método `SayHello` assíncrono.</span><span class="sxs-lookup"><span data-stu-id="6f76b-216">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="6f76b-217">O resultado da chamada `SayHello` é exibido:</span><span class="sxs-lookup"><span data-stu-id="6f76b-217">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="6f76b-218">Testar o cliente gRPC com o serviço de Boas-vindas do gRPC</span><span class="sxs-lookup"><span data-stu-id="6f76b-218">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6f76b-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f76b-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6f76b-220">No serviço Greeter, pressione `Ctrl+F5` para iniciar o servidor sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="6f76b-220">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="6f76b-221">No projeto `GrpcGreeterClient`, pressione `Ctrl+F5` para iniciar o cliente sem o depurador.</span><span class="sxs-lookup"><span data-stu-id="6f76b-221">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6f76b-222">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f76b-222">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6f76b-223">Inicie o serviço Greeter.</span><span class="sxs-lookup"><span data-stu-id="6f76b-223">Start the Greeter service.</span></span>
* <span data-ttu-id="6f76b-224">Inicie o cliente.</span><span class="sxs-lookup"><span data-stu-id="6f76b-224">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6f76b-225">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6f76b-225">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6f76b-226">Inicie o serviço Greeter.</span><span class="sxs-lookup"><span data-stu-id="6f76b-226">Start the Greeter service.</span></span>
* <span data-ttu-id="6f76b-227">Inicie o cliente.</span><span class="sxs-lookup"><span data-stu-id="6f76b-227">Start the client.</span></span>

---

<span data-ttu-id="6f76b-228">O cliente envia uma saudação para o serviço com uma mensagem contendo seu nome, *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="6f76b-228">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="6f76b-229">O serviço envia a mensagem "Olá, GreeterClient" como uma resposta.</span><span class="sxs-lookup"><span data-stu-id="6f76b-229">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="6f76b-230">A resposta "Olá, GreeterClient" é exibida no prompt de comando:</span><span class="sxs-lookup"><span data-stu-id="6f76b-230">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="6f76b-231">O serviço gRPC registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando:</span><span class="sxs-lookup"><span data-stu-id="6f76b-231">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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
> <span data-ttu-id="6f76b-232">O código neste artigo requer o certificado de desenvolvimento HTTPS do ASP.NET Core para proteger o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="6f76b-232">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="6f76b-233">Se o cliente .NET gRPC falhar com a mensagem `The remote certificate is invalid according to the validation procedure.` ou `The SSL connection could not be established.` , o certificado de desenvolvimento não será confiável.</span><span class="sxs-lookup"><span data-stu-id="6f76b-233">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="6f76b-234">Para corrigir esse problema, consulte [chamar um serviço gRPC com um certificado não confiável/inválido](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span><span class="sxs-lookup"><span data-stu-id="6f76b-234">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="6f76b-235">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="6f76b-235">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
