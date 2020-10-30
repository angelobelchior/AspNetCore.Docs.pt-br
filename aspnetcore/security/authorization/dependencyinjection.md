---
title: Injeção de dependência em manipuladores de requisitos no ASP.NET Core
author: rick-anderson
description: Saiba como injetar manipuladores de requisitos de autorização em um aplicativo ASP.NET Core usando injeção de dependência.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060255"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Injeção de dependência em manipuladores de requisitos no ASP.NET Core

<a name="security-authorization-di"></a>

Os [manipuladores de autorização devem ser registrados](xref:security/authorization/policies#handler-registration) na coleção de serviços durante a configuração usando a [injeção de dependência](xref:fundamentals/dependency-injection).

Suponha que você tenha um repositório de regras que deseja avaliar dentro de um manipulador de autorização e que o repositório tenha sido registrado na coleção de serviços. A autorização resolve e injeta isso no construtor.

Por exemplo, para usar ASP. A infraestrutura de registro em log da rede, injeta `ILoggerFactory` no manipulador. Esse manipulador pode ser semelhante ao seguinte código:

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

O manipulador anterior pode ser registrado com qualquer [tempo de vida do serviço](/dotnet/core/extensions/dependency-injection#service-lifetimes). O código a seguir usa `AddSingleton` para registrar o manipulador anterior:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Uma instância do manipulador é criada quando o aplicativo é iniciado e DI injeta o registrado `ILoggerFactory` no construtor.

> [!NOTE]
> Os manipuladores que usam Entity Framework não devem ser registrados como singletons.
