---
title: ASP.NET Core teste de carga/estresse
author: Jeremy-Meng
description: Saiba mais sobre várias ferramentas e abordagens notáveis para teste de carga e teste de estresse ASP.NET Core aplicativos.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: test/loadtests
ms.openlocfilehash: 56f5a5caeea7581e26f8d8cec9662f439cd24b9e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060710"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="7613b-103">ASP.NET Core teste de carga/estresse</span><span class="sxs-lookup"><span data-stu-id="7613b-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="7613b-104">Testes de carga e testes de estresse são importantes para garantir que um aplicativo Web seja eficaz e escalonável.</span><span class="sxs-lookup"><span data-stu-id="7613b-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="7613b-105">Suas metas são diferentes, embora elas geralmente compartilhem testes semelhantes.</span><span class="sxs-lookup"><span data-stu-id="7613b-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="7613b-106">**Testes de carga** : teste se o aplicativo pode tratar de uma carga especificada de usuários para um determinado cenário e ainda atender à meta de resposta.</span><span class="sxs-lookup"><span data-stu-id="7613b-106">**Load tests** : Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="7613b-107">O aplicativo é executado sob condições normais.</span><span class="sxs-lookup"><span data-stu-id="7613b-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="7613b-108">**Testes de estresse** : teste a estabilidade do aplicativo ao executar sob condições extremas, geralmente por um longo período de tempo.</span><span class="sxs-lookup"><span data-stu-id="7613b-108">**Stress tests** : Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="7613b-109">Os testes colocam alta carga de usuário, picos ou aumentam a carga gradualmente, no aplicativo, ou limitam os recursos de computação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7613b-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="7613b-110">Testes de estresse determinam se um aplicativo sob estresse pode se recuperar de uma falha e retornar normalmente para o comportamento esperado.</span><span class="sxs-lookup"><span data-stu-id="7613b-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="7613b-111">Sob estresse, o aplicativo não é executado sob condições normais.</span><span class="sxs-lookup"><span data-stu-id="7613b-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="7613b-112">O Visual Studio 2019 anunciou planos para [substituir o teste de carga](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="7613b-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="7613b-113">O serviço de teste de carga baseado em nuvem DevOps do Azure correspondente foi fechado.</span><span class="sxs-lookup"><span data-stu-id="7613b-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="7613b-114">Ferramentas de terceiros</span><span class="sxs-lookup"><span data-stu-id="7613b-114">Third-party tools</span></span>

<span data-ttu-id="7613b-115">A lista a seguir contém ferramentas de desempenho da Web de terceiros com vários conjuntos de recursos:</span><span class="sxs-lookup"><span data-stu-id="7613b-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="7613b-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="7613b-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="7613b-117">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="7613b-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="7613b-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="7613b-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="7613b-119">K6</span><span class="sxs-lookup"><span data-stu-id="7613b-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="7613b-120">Locust</span><span class="sxs-lookup"><span data-stu-id="7613b-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="7613b-121">Websurto de vento oeste</span><span class="sxs-lookup"><span data-stu-id="7613b-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="7613b-122">Netling</span><span class="sxs-lookup"><span data-stu-id="7613b-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="7613b-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="7613b-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)