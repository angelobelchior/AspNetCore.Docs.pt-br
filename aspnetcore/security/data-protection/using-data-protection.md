---
title: Introdução às APIs de proteção de dados no ASP.NET Core
author: rick-anderson
description: Saiba como usar o ASP.NET Core APIs de proteção de dados para proteger e desproteger dados em um aplicativo.
ms.author: riande
ms.date: 11/12/2019
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
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 0d088e0e974742e51d9ca39a5cec5b84b46f5d21
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022427"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>Introdução às APIs de proteção de dados no ASP.NET Core

<a name="security-data-protection-getting-started"></a>

Em sua forma mais simples, proteger os dados consiste nas seguintes etapas:

1. Crie um protetor de dados de um provedor de proteção de dados.

2. Chame o `Protect` método com os dados que você deseja proteger.

3. Chame o `Unprotect` método com os dados que você deseja voltar para o texto sem formatação.

A maioria das estruturas e modelos de aplicativos, como ASP.NET Core ou SignalR , já configura o sistema de proteção de dados e o adiciona a um contêiner de serviço que você acessa por meio de injeção de dependência. O exemplo a seguir demonstra a configuração de um contêiner de serviço para injeção de dependência e o registro da pilha de proteção de dados, o recebimento do provedor de proteção de dados por meio de DI, a criação de um protetor e a proteção e desproteção de dados.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Ao criar um protetor, você deve fornecer uma ou mais [cadeias de caracteres de finalidade](xref:security/data-protection/consumer-apis/purpose-strings). Uma cadeia de caracteres de finalidade fornece isolamento entre os consumidores. Por exemplo, um protetor criado com uma cadeia de caracteres de finalidade "verde" não seria capaz de desproteger os dados fornecidos por um protetor com a finalidade de "roxo".

>[!TIP]
> Instâncias do `IDataProtectionProvider` e `IDataProtector` são thread-safe para vários chamadores. É intencional que, uma vez que um componente obtenha uma referência a um `IDataProtector` por meio de uma chamada para `CreateProtector` , ele usará essa referência para várias chamadas para `Protect` e `Unprotect` .
>
>Uma chamada para `Unprotect` emitirá CryptographicException se a carga protegida não puder ser verificada ou decifrada. Alguns componentes podem querer ignorar erros durante operações de desproteção; um componente que lê cookie os s de autenticação pode lidar com esse erro e tratar a solicitação como se ela tivesse nenhuma, cookie em vez de falhar a solicitação imediatamente. Os componentes que desejam esse comportamento devem capturar especificamente CryptographicException em vez de assimilar todas as exceções.
