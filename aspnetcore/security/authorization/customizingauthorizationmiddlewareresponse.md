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
# <a name="customize-the-behavior-of-authorizationmiddleware"></a>Personalizar o comportamento do AuthorizationMiddleware

Os aplicativos podem registrar um `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` para personalizar a maneira como o middleware lida com os resultados da autorização. Os aplicativos podem usar o middleware personalizado para:

* Retornar respostas personalizadas.
* Aprimore o desafio padrão ou proíba as respostas.

O código a seguir mostra um exemplo de um manipulador de autorização que retorna uma resposta personalizada para determinados tipos de falhas de autorização:

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

Registrar `MyAuthorizationMiddlewareResultHandler` em `Startup.ConfigureServices` :

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->