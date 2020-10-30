---
title: Provedores de proteção de dados efêmeras no ASP.NET Core
author: rick-anderson
description: Aprenda detalhes de implementação do ASP.NET Core provedores de proteção de dados efêmeras.
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
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: ed0fca88ecf2b002a4421fb120d90adff1b5b12e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052691"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a><span data-ttu-id="b04d6-103">Provedores de proteção de dados efêmeras no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b04d6-103">Ephemeral data protection providers in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-ephemeral"></a>

<span data-ttu-id="b04d6-104">Há cenários em que um aplicativo precisa de um descartável `IDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="b04d6-104">There are scenarios where an application needs a throwaway `IDataProtectionProvider`.</span></span> <span data-ttu-id="b04d6-105">Por exemplo, o desenvolvedor pode simplesmente estar experimentando um aplicativo de console único ou o próprio aplicativo é transitório (ele é inserido no script ou em um projeto de teste de unidade).</span><span class="sxs-lookup"><span data-stu-id="b04d6-105">For example, the developer might just be experimenting in a one-off console application, or the application itself is transient (it's scripted or a unit test project).</span></span> <span data-ttu-id="b04d6-106">Para dar suporte a esses cenários, o pacote [Microsoft. AspNetCore. dataprotection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) inclui um tipo `EphemeralDataProtectionProvider` .</span><span class="sxs-lookup"><span data-stu-id="b04d6-106">To support these scenarios the [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) package includes a type `EphemeralDataProtectionProvider`.</span></span> <span data-ttu-id="b04d6-107">Esse tipo fornece uma implementação básica de `IDataProtectionProvider` cujo repositório de chaves é mantido apenas na memória e não é gravado em nenhum repositório de backup.</span><span class="sxs-lookup"><span data-stu-id="b04d6-107">This type provides a basic implementation of `IDataProtectionProvider` whose key repository is held solely in-memory and isn't written out to any backing store.</span></span>

<span data-ttu-id="b04d6-108">Cada instância do `EphemeralDataProtectionProvider` usa sua própria chave mestra exclusiva.</span><span class="sxs-lookup"><span data-stu-id="b04d6-108">Each instance of `EphemeralDataProtectionProvider` uses its own unique master key.</span></span> <span data-ttu-id="b04d6-109">Portanto, se uma `IDataProtector` raiz em um `EphemeralDataProtectionProvider` gerar uma carga protegida, essa carga só poderá ser desprotegida por um equivalente `IDataProtector` (dado a mesma cadeia de [finalidade](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) ) com raiz na mesma `EphemeralDataProtectionProvider` instância.</span><span class="sxs-lookup"><span data-stu-id="b04d6-109">Therefore, if an `IDataProtector` rooted at an `EphemeralDataProtectionProvider` generates a protected payload, that payload can only be unprotected by an equivalent `IDataProtector` (given the same [purpose](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) chain) rooted at the same `EphemeralDataProtectionProvider` instance.</span></span>

<span data-ttu-id="b04d6-110">O exemplo a seguir demonstra como instanciar um `EphemeralDataProtectionProvider` e usá-lo para proteger e desproteger dados.</span><span class="sxs-lookup"><span data-stu-id="b04d6-110">The following sample demonstrates instantiating an `EphemeralDataProtectionProvider` and using it to protect and unprotect data.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
