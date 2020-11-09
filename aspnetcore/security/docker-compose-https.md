---
title: Hospedagem de ASP.NET Core imagem no contêiner usando o Docker Compose com HTTPS
author: ravipal
description: Saiba como hospedar ASP.NET Core imagens com Docker Compose por HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
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
uid: security/docker-compose-https
ms.openlocfilehash: 37a0142dac1e26afd26dbf2aad46bee20693652e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051636"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="7cdb1-103">Hospedando ASP.NET Core imagens com Docker Compose via HTTPS</span><span class="sxs-lookup"><span data-stu-id="7cdb1-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="7cdb1-104">O ASP.NET Core usa [https por padrão](./enforcing-ssl.md).</span><span class="sxs-lookup"><span data-stu-id="7cdb1-104">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="7cdb1-105">O [https](https://en.wikipedia.org/wiki/HTTPS) depende de [certificados](https://en.wikipedia.org/wiki/Public_key_certificate) para confiança, identidade e criptografia.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="7cdb1-106">Este documento explica como executar imagens de contêiner predefinidas com HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="7cdb1-107">Consulte [desenvolvendo aplicativos ASP.NET Core com o Docker sobre HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) para cenários de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="7cdb1-108">Este exemplo requer o [docker 17, 6](https://docs.docker.com/release-notes/docker-ce) ou posterior do [cliente do Docker](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="7cdb1-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7cdb1-109">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="7cdb1-109">Prerequisites</span></span>

<span data-ttu-id="7cdb1-110">O [SDK do .NET Core 2,2](https://dotnet.microsoft.com/download) ou posterior é necessário para algumas das instruções neste documento.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="7cdb1-111">Certificados</span><span class="sxs-lookup"><span data-stu-id="7cdb1-111">Certificates</span></span>

<span data-ttu-id="7cdb1-112">Um certificado de uma [autoridade de certificação](https://wikipedia.org/wiki/Certificate_authority) é necessário para [Hospedagem de produção](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) para um domínio.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="7cdb1-113">[Let's Encrypt](https://letsencrypt.org/) é uma autoridade de certificação que oferece certificados gratuitos.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="7cdb1-114">Este documento usa [certificados de desenvolvimento autoassinados](https://wikipedia.org/wiki/Self-signed_certificate) para hospedar imagens predefinidas `localhost` .</span><span class="sxs-lookup"><span data-stu-id="7cdb1-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="7cdb1-115">As instruções são semelhantes ao uso de certificados de produção.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="7cdb1-116">Para certificados de produção:</span><span class="sxs-lookup"><span data-stu-id="7cdb1-116">For production certificates:</span></span>

* <span data-ttu-id="7cdb1-117">A `dotnet dev-certs` ferramenta não é necessária.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="7cdb1-118">Os certificados não precisam ser armazenados no local usado nas instruções.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="7cdb1-119">Armazene os certificados em qualquer local fora do diretório do site.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="7cdb1-120">As instruções contidas na seção a seguir montam os certificados de montagem em contêineres usando a `volumes` propriedade em *Docker-Compose. yml.*</span><span class="sxs-lookup"><span data-stu-id="7cdb1-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="7cdb1-121">Você pode adicionar certificados em imagens de contêiner com um `COPY` comando em um *Dockerfile* , mas isso não é recomendado.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-121">You could add certificates into container images with a `COPY` command in a *Dockerfile* , but it's not recommended.</span></span> <span data-ttu-id="7cdb1-122">A cópia de certificados em uma imagem não é recomendada pelos seguintes motivos:</span><span class="sxs-lookup"><span data-stu-id="7cdb1-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="7cdb1-123">Isso dificulta o uso da mesma imagem para teste com certificados de desenvolvedor.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="7cdb1-124">Isso dificulta o uso da mesma imagem para hospedagem com certificados de produção.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="7cdb1-125">Há um risco significativo de divulgação de certificado.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="7cdb1-126">Iniciando um contêiner com suporte a https usando o Docker Compose</span><span class="sxs-lookup"><span data-stu-id="7cdb1-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="7cdb1-127">Use as instruções a seguir para a configuração do seu sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="7cdb1-128">Windows usando contêineres do Linux</span><span class="sxs-lookup"><span data-stu-id="7cdb1-128">Windows using Linux containers</span></span>

<span data-ttu-id="7cdb1-129">Gerar certificado e configurar computador local:</span><span class="sxs-lookup"><span data-stu-id="7cdb1-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="7cdb1-130">Nos comandos anteriores, substitua `{ password here }` por uma senha.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="7cdb1-131">Crie um arquivo _Docker-Compose. Debug. yml_ com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="7cdb1-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="7cdb1-132">A senha especificada no arquivo Docker Compose deve corresponder à senha usada para o certificado.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="7cdb1-133">Inicie o contêiner com ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="7cdb1-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="7cdb1-134">macOS ou Linux</span><span class="sxs-lookup"><span data-stu-id="7cdb1-134">macOS or Linux</span></span>

<span data-ttu-id="7cdb1-135">Gerar certificado e configurar computador local:</span><span class="sxs-lookup"><span data-stu-id="7cdb1-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="7cdb1-136">`dotnet dev-certs https --trust` Só tem suporte no macOS e no Windows.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="7cdb1-137">Você precisa confiar em certificados no Linux na forma com que a sua distribuição dá suporte.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-137">You need to trust certificates on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="7cdb1-138">É provável que você precise confiar no certificado em seu navegador.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="7cdb1-139">Nos comandos anteriores, substitua `{ password here }` por uma senha.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="7cdb1-140">Crie um arquivo _Docker-Compose. Debug. yml_ com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="7cdb1-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="7cdb1-141">A senha especificada no arquivo Docker Compose deve corresponder à senha usada para o certificado.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="7cdb1-142">Inicie o contêiner com ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="7cdb1-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="7cdb1-143">Windows usando contêineres do Windows</span><span class="sxs-lookup"><span data-stu-id="7cdb1-143">Windows using Windows containers</span></span>

<span data-ttu-id="7cdb1-144">Gerar certificado e configurar computador local:</span><span class="sxs-lookup"><span data-stu-id="7cdb1-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="7cdb1-145">Nos comandos anteriores, substitua `{ password here }` por uma senha.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="7cdb1-146">Crie um arquivo _Docker-Compose. Debug. yml_ com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="7cdb1-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
<span data-ttu-id="7cdb1-147">A senha especificada no arquivo Docker Compose deve corresponder à senha usada para o certificado.</span><span class="sxs-lookup"><span data-stu-id="7cdb1-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="7cdb1-148">Inicie o contêiner com ASP.NET Core configurado para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="7cdb1-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
