---
title: Visão geral sobre a segurança do ASP.NET Core
author: rick-anderson
description: Saiba mais sobre conceitos básicos de autenticação, autorização e segurança no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/index
ms.openlocfilehash: 3c86c66bebe8a5ce1c195ebf931193e7e2a73fef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051623"
---
# <a name="overview-of-aspnet-core-security"></a>Visão geral sobre a segurança do ASP.NET Core

O ASP.NET Core permite que desenvolvedores configurem e gerenciem facilmente a segurança de seus aplicativos. O ASP.NET Core contém recursos para gerenciar a autenticação, autorização, proteção de dados, imposição de HTTPS, segredos de aplicativo, prevenção de XSRF/CSRF e gerenciamento de CORS. Esses recursos de segurança permitem que você crie aplicativos de ASP.NET Core robustos e seguros ao mesmo tempo.

## <a name="aspnet-core-security-features"></a>Recursos de segurança do ASP.NET Core

O ASP.NET Core fornece muitas ferramentas e bibliotecas para proteger seus aplicativos, incluindo provedores de identidade internos, mas você pode usar serviços de identidade de terceiros, como Facebook, Twitter e LinkedIn. Com o ASP.NET Core, você pode gerenciar facilmente os segredos do aplicativo, que são uma maneira de armazenar e usar informações confidenciais sem a necessidade de expô-los no código.

## <a name="authentication-vs-authorization"></a>Autenticação vs. autorização

A autenticação é um processo em que um usuário fornece credenciais que são comparadas àquelas armazenadas em um sistema operacional, num banco de dados, no aplicativo ou no recurso. Se elas corresponderem, os usuários se autenticarão com êxito e, assim, poderão realizar ações para as quais são autorizados, durante um processo de autorização. A autorização é o processo que determina o que um usuário pode fazer.

Outra forma de pensar na autenticação é considerá-la como uma maneira de entrar em um espaço, como um servidor, um banco de dados, um aplicativo ou um recurso, ao passo que a autorização refere-se a quais ações o usuário poderá executar em que objetos dentro desse espaço (servidor, banco de dados ou aplicativo).

## <a name="common-vulnerabilities-in-software"></a>Vulnerabilidades comuns no software

O ASP.NET Core e o EF contêm recursos que ajudam a proteger seus aplicativos e impedir violações de segurança. A seguinte lista de links leva à documentação com detalhe de técnicas para evitar as vulnerabilidades de segurança mais comuns em aplicativos Web:

* [Ataques XSS (script entre sites)](xref:security/cross-site-scripting)
* [Ataques de injeção de SQL](/ef/core/querying/raw-sql)
* [Ataques de solicitação entre sites forjado (XSRF/CSRF)](xref:security/anti-request-forgery)
* [Ataques de redirecionamento aberto](xref:security/preventing-open-redirects)

Há mais vulnerabilidades sobre as quais você deve estar atento. Para obter mais informações, consulte os outros artigos na seção **segurança Identity e** do Sumário.
