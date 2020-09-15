---
title: Configurar o corte para ASP.NET Core Blazor
author: guardrex
description: Saiba como controlar o vinculador (corte) de IL (linguagem intermediária) ao criar um Blazor aplicativo.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 2923f76c586465e4e6044763f18527a7d36ad57c
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080860"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a><span data-ttu-id="66ac1-103">Configurar o corte para ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="66ac1-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="66ac1-104">Por [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="66ac1-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="66ac1-105">Blazor WebAssembly executa a corte de [Il (linguagem intermediária)](/dotnet/standard/managed-code#intermediate-language--execution) para reduzir o tamanho da saída publicada.</span><span class="sxs-lookup"><span data-stu-id="66ac1-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span>

<span data-ttu-id="66ac1-106">Aparar um aplicativo otimiza o tamanho, mas pode ter efeitos prejudiciais.</span><span class="sxs-lookup"><span data-stu-id="66ac1-106">Trimming an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="66ac1-107">Os aplicativos que usam reflexão ou recursos dinâmicos relacionados podem interromper quando cortados porque o corte não conhece o comportamento dinâmico e não pode determinar em geral quais tipos são necessários para reflexão no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="66ac1-107">Apps that use reflection or related dynamic features may break when trimmed because the trimmer doesn't know about dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="66ac1-108">Para aparar esses aplicativos, o corte deve ser informado sobre quaisquer tipos exigidos pela reflexão no código e em pacotes ou estruturas das quais o aplicativo depende.</span><span class="sxs-lookup"><span data-stu-id="66ac1-108">To trim such apps, the trimmer must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="66ac1-109">Para garantir que o aplicativo cortado funcione corretamente depois de implantado, é importante testar a saída publicada com frequência ao desenvolver.</span><span class="sxs-lookup"><span data-stu-id="66ac1-109">To ensure the trimmed app works correctly once deployed, it's important to test published output frequently while developing.</span></span>

<span data-ttu-id="66ac1-110">A remoção de aplicativos .NET pode ser desabilitada definindo a `PublishTrimmed` Propriedade MSBuild como `false` no arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="66ac1-110">Trimming for .NET apps can be disabled by setting the `PublishTrimmed` MSBuild property to `false` in the app's project file:</span></span>

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```

## <a name="additional-resources"></a><span data-ttu-id="66ac1-111">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="66ac1-111">Additional resources</span></span>

* [<span data-ttu-id="66ac1-112">Cortar implantações e executáveis autossuficientes</span><span class="sxs-lookup"><span data-stu-id="66ac1-112">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
