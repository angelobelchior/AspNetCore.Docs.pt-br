---
title: Principais configurações de imutabilidade e chave em ASP.NET Core
author: rick-anderson
description: Conheça os detalhes de implementação das APIs de imutabilidade da chave de proteção de dados ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 8efca1d96945cb7af0132b27801b23a45714e08a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061243"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a><span data-ttu-id="b256b-103">Principais configurações de imutabilidade e chave em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b256b-103">Key immutability and key settings in ASP.NET Core</span></span>

<span data-ttu-id="b256b-104">Depois que um objeto é persistido no armazenamento de backup, sua representação é sempre fixa.</span><span class="sxs-lookup"><span data-stu-id="b256b-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="b256b-105">Novos dados podem ser adicionados ao repositório de backup, mas os dados existentes nunca podem ser modificados.</span><span class="sxs-lookup"><span data-stu-id="b256b-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="b256b-106">A principal finalidade desse comportamento é evitar a corrupção de dados.</span><span class="sxs-lookup"><span data-stu-id="b256b-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="b256b-107">Uma consequência desse comportamento é que, uma vez que uma chave é gravada no armazenamento de backup, ela é imutável.</span><span class="sxs-lookup"><span data-stu-id="b256b-107">One consequence of this behavior is that once a key is written to the backing store, it's immutable.</span></span> <span data-ttu-id="b256b-108">Suas datas de criação, ativação e expiração nunca podem ser alteradas, embora possam ser revogadas usando o `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="b256b-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="b256b-109">Além disso, suas informações de algoritmos subjacentes, material de chaveamento mestre e propriedades de criptografia em repouso também são imutáveis.</span><span class="sxs-lookup"><span data-stu-id="b256b-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="b256b-110">Se o desenvolvedor alterar qualquer configuração que afete a persistência de chave, essas alterações não entrarão em vigor até a próxima vez que uma chave for gerada, seja por meio de uma chamada explícita para `IKeyManager.CreateNewKey` ou por meio do próprio comportamento de [geração de chave automática](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) do sistema de proteção de dados.</span><span class="sxs-lookup"><span data-stu-id="b256b-110">If the developer changes any setting that affects key persistence, those changes won't go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="b256b-111">As configurações que afetam a persistência de chave são as seguintes:</span><span class="sxs-lookup"><span data-stu-id="b256b-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="b256b-112">O tempo de vida da chave padrão</span><span class="sxs-lookup"><span data-stu-id="b256b-112">The default key lifetime</span></span>](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [<span data-ttu-id="b256b-113">O mecanismo de criptografia de chave em repouso</span><span class="sxs-lookup"><span data-stu-id="b256b-113">The key encryption at rest mechanism</span></span>](xref:security/data-protection/implementation/key-encryption-at-rest)

* [<span data-ttu-id="b256b-114">As informações de algoritmos contidas na chave</span><span class="sxs-lookup"><span data-stu-id="b256b-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="b256b-115">Se você precisar que essas configurações sejam ativadas antes do próximo tempo de reversão automático da chave, considere fazer uma chamada explícita para `IKeyManager.CreateNewKey` para forçar a criação de uma nova chave.</span><span class="sxs-lookup"><span data-stu-id="b256b-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="b256b-116">Lembre-se de fornecer uma data de ativação explícita ({Now + 2 dias} é uma boa regra geral para permitir o tempo para a alteração ser propagada) e a data de expiração na chamada.</span><span class="sxs-lookup"><span data-stu-id="b256b-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="b256b-117">Todos os aplicativos que tocam no repositório devem especificar as mesmas configurações com os `IDataProtectionBuilder` métodos de extensão.</span><span class="sxs-lookup"><span data-stu-id="b256b-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="b256b-118">Caso contrário, as propriedades da chave persistente serão dependentes do aplicativo específico que invocou as rotinas de geração de chave.</span><span class="sxs-lookup"><span data-stu-id="b256b-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
