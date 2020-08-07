---
title: Diversas APIs de proteção de dados de ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a interface ASP.NET Core Data Protection ISecret.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 2e319cdcec1e005682555c4e03c52632e6d8521a
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913813"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>Diversas APIs de proteção de dados de ASP.NET Core

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Os tipos que implementam qualquer uma das interfaces a seguir devem ser thread-safe para vários chamadores.

## <a name="isecret"></a>ISecret

A `ISecret` interface representa um valor secreto, como o material de chave de criptografia. Ele contém a seguinte superfície de API:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

O `WriteSecretIntoBuffer` método popula o buffer fornecido com o valor de segredo bruto. O motivo pelo qual essa API usa o buffer como um parâmetro em vez de retornar um `byte[]` diretamente é que isso dá ao chamador a oportunidade de fixar o objeto de buffer, limitando a exposição do segredo ao coletor de lixo gerenciado.

O `Secret` tipo é uma implementação concreta de `ISecret` onde o valor secreto é armazenado na memória em processo. Em plataformas Windows, o valor secreto é criptografado por meio de [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).
