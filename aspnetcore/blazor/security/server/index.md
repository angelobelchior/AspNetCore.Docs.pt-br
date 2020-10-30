---
title: Proteger ASP.NET Core Blazor Server aplicativos
author: guardrex
description: Saiba como proteger aplicativos Blazor Server como ASP.NET Core aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 108fb3a8a24295cad43fd8c83303abd95a7ecd33
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055471"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a>Proteger ASP.NET Core Blazor Server aplicativos

Por [Luke Latham](https://github.com/guardrex)

Blazor Server os aplicativos são configurados para segurança da mesma maneira que ASP.NET Core aplicativos. Para obter mais informações, consulte os artigos em <xref:security/index> . Os tópicos desta visão geral se aplicam especificamente ao Blazor Server .

## <a name="no-locblazor-server-project-template"></a>Blazor Server modelo de projeto

O Blazor Server modelo de projeto pode ser configurado para autenticação quando o projeto é criado.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Siga as diretrizes do Visual Studio no <xref:blazor/tooling> para criar um novo Blazor Server projeto com um mecanismo de autenticação.

Depois de escolher o modelo de **Blazor Server aplicativo** na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione **alterar** em **autenticação** .

Uma caixa de diálogo é aberta para oferecer o mesmo conjunto de mecanismos de autenticação para outros projetos ASP.NET Core:

* **Sem autenticação**
* **Contas de usuário individuais** : as contas de usuário podem ser armazenadas:
  * Dentro do aplicativo usando o sistema do ASP.NET Core [Identity](xref:security/authentication/identity) .
  * Com o [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Contas corporativas ou de estudante**
* **Autenticação do Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Siga as diretrizes de Visual Studio Code no <xref:blazor/tooling> para criar um novo Blazor Server projeto com um mecanismo de autenticação:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.

| Mecanismo de autenticação | Descrição |
| ------------------------ | ----------- |
| `None` (padrão)         | Sem autenticação |
| `Individual`             | Usuários armazenados no aplicativo com ASP.NET Core Identity |
| `IndividualB2C`          | Usuários armazenados no [Azure ad B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Autenticação organizacional para um único locatário |
| `MultiOrg`               | Autenticação organizacional para vários locatários |
| `Windows`                | Autenticação do Windows |

Usando a `-o|--output` opção, o comando usa o valor fornecido para o `{APP NAME}` espaço reservado para:

* Crie uma pasta para o projeto.
* Nomeie o projeto.

Para obter mais informações, consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Siga as diretrizes de Visual Studio para Mac no <xref:blazor/tooling> .

1. Na etapa **configurar seu novo Blazor Server aplicativo** , selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .

1. O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core Identity .

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Crie um novo Blazor Server projeto com um mecanismo de autenticação usando o seguinte comando em um shell de comando:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.

| Mecanismo de autenticação | Descrição |
| ------------------------ | ----------- |
| `None` (padrão)         | Sem autenticação |
| `Individual`             | Usuários armazenados no aplicativo com ASP.NET Core Identity |
| `IndividualB2C`          | Usuários armazenados no [Azure ad B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Autenticação organizacional para um único locatário |
| `MultiOrg`               | Autenticação organizacional para vários locatários |
| `Windows`                | Autenticação do Windows |

Usando a `-o|--output` opção, o comando usa o valor fornecido para o `{APP NAME}` espaço reservado para:

* Crie uma pasta para o projeto.
* Nomeie o projeto.

Para mais informações:

* Consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.
* Execute o comando de ajuda para o Blazor Server modelo ( `blazorserver` ) em um shell de comando:

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a>Scaffold Identity

Scaffold Identity em um Blazor Server projeto:

* [Sem autorização existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [Com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).

## <a name="additional-resources"></a>Recursos adicionais

* [Início Rápido: Adicionar entrada com a Microsoft para um aplicativo Web do ASP.NET Core](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [Início Rápido: Proteger uma API Web ASP.NET Core com a plataforma de identidade da Microsoft](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
