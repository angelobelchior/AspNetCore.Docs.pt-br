---
title: Personalizar o comportamento do AuthorizationMiddleware
author: pranavkm
ms.author: prkrishn
description: Este artigo explica como personalizar o tratamento de resultados do AuthorizationMiddleware.
monikerRange: '>= aspnetcore-5.0'
uid: security/authorization/authorizationmiddlewareresulthandler
ms.openlocfilehash: 55f4433c080d6ce6676ca1072dacacc137f15638
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92156777"
---
# <a name="customize-the-behavior-of-authorizationmiddleware"></a><span data-ttu-id="64d19-103">Personalizar o comportamento do AuthorizationMiddleware</span><span class="sxs-lookup"><span data-stu-id="64d19-103">Customize the behavior of AuthorizationMiddleware</span></span>

<span data-ttu-id="64d19-104">Os aplicativos podem registrar um `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` para personalizar a maneira como o middleware lida com os resultados da autorização.</span><span class="sxs-lookup"><span data-stu-id="64d19-104">Applications can register a `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` to customize the way the middleware handles the authorization results.</span></span> <span data-ttu-id="64d19-105">Os aplicativos podem usar o middleware personalizado para:</span><span class="sxs-lookup"><span data-stu-id="64d19-105">Applications can use the customized middleware to:</span></span>

* <span data-ttu-id="64d19-106">Retornar respostas personalizadas.</span><span class="sxs-lookup"><span data-stu-id="64d19-106">Return customized responses.</span></span>
* <span data-ttu-id="64d19-107">Aprimore o desafio padrão ou proíba as respostas.</span><span class="sxs-lookup"><span data-stu-id="64d19-107">Enhance the default challenge or forbid responses.</span></span>

<span data-ttu-id="64d19-108">O código a seguir mostra um exemplo de um manipulador de autorização que retorna uma resposta personalizada para determinados tipos de falhas de autorização:</span><span class="sxs-lookup"><span data-stu-id="64d19-108">The following code shows an example of an authorization handler that returns a custom response for certain kinds of authorization failures:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

<span data-ttu-id="64d19-109">Registrar `MyAuthorizationMiddlewareResultHandler` em `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="64d19-109">Register `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->