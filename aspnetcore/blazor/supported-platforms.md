---
title: ASP.NET Core Blazor plataformas com suporte
author: guardrex
description: Saiba mais sobre as plataformas com suporte para ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 67896bcdd5d99ff2c9cf22e3902262f9513eb4f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013522"
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
