---
title: Injeção de dependência em manipuladores de requisitos no ASP.NET Core
author: rick-anderson
description: Saiba como injetar manipuladores de requisitos de autorização em um aplicativo ASP.NET Core usando injeção de dependência.
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 4bc7eb38262c8a94a84aacc978737a778bfd71a1
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632558"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Injeção de dependência em manipuladores de requisitos no ASP.NET Core

<a name="security-authorization-di"></a>

Os [manipuladores de autorização devem ser registrados](xref:security/authorization/policies#handler-registration) na coleção de serviços durante a configuração (usando a [injeção de dependência](xref:fundamentals/dependency-injection)).

Suponha que você tenha um repositório de regras que deseja avaliar dentro de um manipulador de autorização e que o repositório tenha sido registrado na coleção de serviços. A autorização irá resolver e injetar isso em seu construtor.

Por exemplo, se você quisesse usar o ASP. A infraestrutura de registro em log da rede que você deseja injetar `ILoggerFactory` em seu manipulador. Esse manipulador pode ser semelhante a:

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

Você registraria o manipulador com `services.AddSingleton()` :

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Uma instância do manipulador será criada quando seu aplicativo for iniciado e DI irá injetar o registrado `ILoggerFactory` em seu construtor.

> [!NOTE]
> Os manipuladores que usam Entity Framework não devem ser registrados como singletons.
