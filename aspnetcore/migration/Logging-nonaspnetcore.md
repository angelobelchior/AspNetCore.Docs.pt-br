---
title: Migre de Microsoft. Extensions. Logging 2,1 para 2,2 ou 3,0
author: pakrym
description: Saiba como migrar um aplicativo non-ASP.NET Core que usa Microsoft. Extensions. Logging de 2,1 para 2,2 ou 3,0.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
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
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 8ef07118e3a885e41221402bdfc2d7e942c6dd73
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634092"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="70a0d-103">Migre de Microsoft. Extensions. Logging 2,1 para 2,2 ou 3,0</span><span class="sxs-lookup"><span data-stu-id="70a0d-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="70a0d-104">Este artigo descreve as etapas comuns para migrar um aplicativo non-ASP.NET Core que usa `Microsoft.Extensions.Logging` de 2,1 para 2,2 ou 3,0.</span><span class="sxs-lookup"><span data-stu-id="70a0d-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="70a0d-105">2.1 a 2.2</span><span class="sxs-lookup"><span data-stu-id="70a0d-105">2.1 to 2.2</span></span>

<span data-ttu-id="70a0d-106">Criar `ServiceCollection` e chamar manualmente `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="70a0d-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="70a0d-107">2,1 exemplo:</span><span class="sxs-lookup"><span data-stu-id="70a0d-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="70a0d-108">2,2 exemplo:</span><span class="sxs-lookup"><span data-stu-id="70a0d-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="70a0d-109">2,1 a 3,0</span><span class="sxs-lookup"><span data-stu-id="70a0d-109">2.1 to 3.0</span></span>

<span data-ttu-id="70a0d-110">Em 3,0, use `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="70a0d-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="70a0d-111">2,1 exemplo:</span><span class="sxs-lookup"><span data-stu-id="70a0d-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="70a0d-112">3,0 exemplo:</span><span class="sxs-lookup"><span data-stu-id="70a0d-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="70a0d-113">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="70a0d-113">Additional resources</span></span>

* <span data-ttu-id="70a0d-114">[Pacote NuGet Microsoft. Extensions. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="70a0d-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
