---
title: SDK do ASP.NET Core Web
author: Rick-Anderson
description: Visão geral de Microsoft. NET. Sdk. Web.
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: a717d9dc0e326e62ec920d2a838e8b18be3db013
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020685"
---
# <a name="aspnet-core-web-sdk"></a>SDK do ASP.NET Core Web

### <a name="overview"></a>Visão geral

`Microsoft.NET.Sdk.Web`é um [SDK de projeto do MSBuild](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) para compilar ASP.NET Core aplicativos. É possível criar um aplicativo ASP.NET Core sem esse SDK, no entanto, o SDK da Web é:

* Adaptado em direção à oferta de uma experiência de primeira classe.
* O destino recomendado para a maioria dos usuários.

Use o Web. SDK em um projeto do:

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

Recursos habilitados usando o SDK da Web:

* Projetos voltados para o .NET Core 3,0 ou posterior referência implícita:

  * A [estrutura compartilhada ASP.NET Core](xref:fundamentals/metapackage-app).
  * [Analisadores](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) projetados para a criação de aplicativos ASP.NET Core.
* O SDK da Web importa destinos do MSBuild que habilitam o uso de perfis de publicação e publicação usando o WebDeploy.

### <a name="properties"></a>Propriedades

| Propriedade | Descrição |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | Desabilita a referência implícita à `Microsoft.AspNetCore.App` estrutura compartilhada. |
| `DisableImplicitAspNetCoreAnalyzers` | Desabilita a referência implícita a ASP.NET Core analisadores. |
| `DisableImplicitComponentsAnalyzers` | Desabilita a referência implícita aos Razor analisadores de componentes durante a Blazor compilação de aplicativos (servidor). |
