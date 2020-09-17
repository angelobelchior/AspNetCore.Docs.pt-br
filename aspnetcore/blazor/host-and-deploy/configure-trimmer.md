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
ms.openlocfilehash: 57d8f069c79b558020253968d736f350bc8a6f03
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721730"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a><span data-ttu-id="215c2-103">Configurar o corte para ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="215c2-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="215c2-104">Por [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="215c2-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="215c2-105">Blazor WebAssembly executa a corte de [Il (linguagem intermediária)](/dotnet/standard/managed-code#intermediate-language--execution) para reduzir o tamanho da saída publicada.</span><span class="sxs-lookup"><span data-stu-id="215c2-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span>

<span data-ttu-id="215c2-106">Aparar um aplicativo otimiza o tamanho, mas pode ter efeitos prejudiciais.</span><span class="sxs-lookup"><span data-stu-id="215c2-106">Trimming an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="215c2-107">Os aplicativos que usam reflexão ou recursos dinâmicos relacionados podem interromper quando cortados porque o corte não conhece o comportamento dinâmico e não pode determinar em geral quais tipos são necessários para reflexão no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="215c2-107">Apps that use reflection or related dynamic features may break when trimmed because the trimmer doesn't know about dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="215c2-108">Para aparar esses aplicativos, o corte deve ser informado sobre quaisquer tipos exigidos pela reflexão no código e em pacotes ou estruturas das quais o aplicativo depende.</span><span class="sxs-lookup"><span data-stu-id="215c2-108">To trim such apps, the trimmer must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="215c2-109">Para garantir que o aplicativo cortado funcione corretamente depois de implantado, é importante testar a saída publicada com frequência ao desenvolver.</span><span class="sxs-lookup"><span data-stu-id="215c2-109">To ensure the trimmed app works correctly once deployed, it's important to test published output frequently while developing.</span></span>

<span data-ttu-id="215c2-110">A remoção de aplicativos .NET pode ser desabilitada definindo a `PublishTrimmed` Propriedade MSBuild como `false` no arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="215c2-110">Trimming for .NET apps can be disabled by setting the `PublishTrimmed` MSBuild property to `false` in the app's project file:</span></span>

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
<span data-ttu-id="215c2-111">Opções adicionais para configurar o corte podem ser encontradas em [Opções de corte](/dotnet/core/deploying/trimming-options).</span><span class="sxs-lookup"><span data-stu-id="215c2-111">Additional options to configure the trimmer can be found at [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="215c2-112">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="215c2-112">Additional resources</span></span>

* [<span data-ttu-id="215c2-113">Cortar implantações e executáveis autossuficientes</span><span class="sxs-lookup"><span data-stu-id="215c2-113">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
