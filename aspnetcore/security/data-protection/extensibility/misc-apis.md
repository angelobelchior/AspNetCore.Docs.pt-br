---
title: Diversas APIs de proteção de dados de ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a interface ASP.NET Core Data Protection ISecret.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- appsettings.json
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
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: bd772b11300cca8896ed512da1cd12c49e6f104b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060749"
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
