---
title: Diversas APIs de proteção de dados de ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a interface ASP.NET Core Data Protection ISecret.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 947c6eb62550d325492365ece84d45a14845888f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630907"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="c0b99-103">Diversas APIs de proteção de dados de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0b99-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="c0b99-104">Os tipos que implementam qualquer uma das interfaces a seguir devem ser thread-safe para vários chamadores.</span><span class="sxs-lookup"><span data-stu-id="c0b99-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="c0b99-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="c0b99-105">ISecret</span></span>

<span data-ttu-id="c0b99-106">A `ISecret` interface representa um valor secreto, como o material de chave de criptografia.</span><span class="sxs-lookup"><span data-stu-id="c0b99-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="c0b99-107">Ele contém a seguinte superfície de API:</span><span class="sxs-lookup"><span data-stu-id="c0b99-107">It contains the following API surface:</span></span>

* <span data-ttu-id="c0b99-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="c0b99-108">`Length`: `int`</span></span>

* <span data-ttu-id="c0b99-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="c0b99-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="c0b99-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="c0b99-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="c0b99-111">O `WriteSecretIntoBuffer` método popula o buffer fornecido com o valor de segredo bruto.</span><span class="sxs-lookup"><span data-stu-id="c0b99-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="c0b99-112">O motivo pelo qual essa API usa o buffer como um parâmetro em vez de retornar um `byte[]` diretamente é que isso dá ao chamador a oportunidade de fixar o objeto de buffer, limitando a exposição do segredo ao coletor de lixo gerenciado.</span><span class="sxs-lookup"><span data-stu-id="c0b99-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="c0b99-113">O `Secret` tipo é uma implementação concreta de `ISecret` onde o valor secreto é armazenado na memória em processo.</span><span class="sxs-lookup"><span data-stu-id="c0b99-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="c0b99-114">Em plataformas Windows, o valor secreto é criptografado por meio de [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span><span class="sxs-lookup"><span data-stu-id="c0b99-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>
