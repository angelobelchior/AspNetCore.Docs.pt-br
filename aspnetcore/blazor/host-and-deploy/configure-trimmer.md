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
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a>Configurar o corte para ASP.NET Core Blazor

Por [Pranav Krishnamoorthy](https://github.com/pranavkm)

Blazor WebAssembly executa a corte de [Il (linguagem intermediária)](/dotnet/standard/managed-code#intermediate-language--execution) para reduzir o tamanho da saída publicada.

Aparar um aplicativo otimiza o tamanho, mas pode ter efeitos prejudiciais. Os aplicativos que usam reflexão ou recursos dinâmicos relacionados podem interromper quando cortados porque o corte não conhece o comportamento dinâmico e não pode determinar em geral quais tipos são necessários para reflexão no tempo de execução. Para aparar esses aplicativos, o corte deve ser informado sobre quaisquer tipos exigidos pela reflexão no código e em pacotes ou estruturas das quais o aplicativo depende.

Para garantir que o aplicativo cortado funcione corretamente depois de implantado, é importante testar a saída publicada com frequência ao desenvolver.

A remoção de aplicativos .NET pode ser desabilitada definindo a `PublishTrimmed` Propriedade MSBuild como `false` no arquivo de projeto do aplicativo:

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```

## <a name="additional-resources"></a>Recursos adicionais

* [Cortar implantações e executáveis autossuficientes](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
