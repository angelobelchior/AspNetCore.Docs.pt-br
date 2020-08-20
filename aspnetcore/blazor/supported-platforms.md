---
title: ASP.NET Core Blazor plataformas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 692ab63bb48dbfa29021d59cdf035e9549d3039c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625941"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>ASP.NET Core Blazor plataformas com suporte

Por [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>Requisitos de navegador

### Blazor WebAssembly

| Navegador                          | Versão               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Current               |
| Mozilla Firefox                  | Current               |
| Google Chrome, incluindo Android | Current               |
| Safari, incluindo iOS            | Current               |
| Microsoft Internet Explorer      | Sem suporte&dagger; |

&dagger;O Microsoft Internet Explorer não dá suporte ao [Webassembly](https://webassembly.org).

### Blazor Server

| Navegador                          | Versão    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Current    |
| Mozilla Firefox                  | Current    |
| Google Chrome, incluindo Android | Current    |
| Safari, incluindo iOS            | Current    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Os suportes retroativos adicionais são necessários (por exemplo, as promessas podem ser adicionadas por meio de um [`Polyfill.io`](https://polyfill.io/v3/) pacote).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:blazor/hosting-models>
