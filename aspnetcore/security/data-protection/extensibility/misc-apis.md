---
title: Diversas APIs de proteção de dados de ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a interface ASP.NET Core Data Protection ISecret.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 5ab8226779fb4209a7254b95eccac4be2d26b10d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019034"
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
