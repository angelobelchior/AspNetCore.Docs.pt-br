---
title: ASP.NET Core teste de carga/estresse
author: Jeremy-Meng
description: Saiba mais sobre várias ferramentas e abordagens notáveis para teste de carga e teste de estresse ASP.NET Core aplicativos.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: test/loadtests
ms.openlocfilehash: 56f5a5caeea7581e26f8d8cec9662f439cd24b9e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060710"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core teste de carga/estresse

Testes de carga e testes de estresse são importantes para garantir que um aplicativo Web seja eficaz e escalonável. Suas metas são diferentes, embora elas geralmente compartilhem testes semelhantes.

**Testes de carga** : teste se o aplicativo pode tratar de uma carga especificada de usuários para um determinado cenário e ainda atender à meta de resposta. O aplicativo é executado sob condições normais.

**Testes de estresse** : teste a estabilidade do aplicativo ao executar sob condições extremas, geralmente por um longo período de tempo. Os testes colocam alta carga de usuário, picos ou aumentam a carga gradualmente, no aplicativo, ou limitam os recursos de computação do aplicativo.

Testes de estresse determinam se um aplicativo sob estresse pode se recuperar de uma falha e retornar normalmente para o comportamento esperado. Sob estresse, o aplicativo não é executado sob condições normais.

O Visual Studio 2019 anunciou planos para [substituir o teste de carga](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/). O serviço de teste de carga baseado em nuvem DevOps do Azure correspondente foi fechado.

## <a name="third-party-tools"></a>Ferramentas de terceiros

A lista a seguir contém ferramentas de desempenho da Web de terceiros com vários conjuntos de recursos:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (AB)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [K6](https://k6.io)
* [Locust](https://locust.io/)
* [Websurto de vento oeste](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)