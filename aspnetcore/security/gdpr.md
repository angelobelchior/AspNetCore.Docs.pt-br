---
title: Suporte a Regulamento Geral sobre a Proteção de Dados (GDPR) no ASP.NET Core
author: rick-anderson
description: Saiba como acessar os pontos de extensão do GDPR em um aplicativo Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: ec65a2c8362c15716bebd6b22f5639785ba74c98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050999"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Suporte a Regulamento Geral sobre a Proteção de Dados da UE (GDPR) no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core fornece APIs e modelos para ajudar a atender a alguns dos requisitos de [regulamento geral sobre a proteção de dados da UE (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :

::: moniker range=">= aspnetcore-3.0"

* Os modelos de projeto incluem pontos de extensão e marcação fragmentado que você pode substituir pela sua política de privacidade e cookie uso.
* A página *Pages/privacy. cshtml* ou *views/Home/privacy. cshtml* fornece uma página para detalhar a política de privacidade do seu site.

Para habilitar o cookie recurso de consentimento padrão como o encontrado nos modelos ASP.NET Core 2,2 em um aplicativo gerado pelo modelo ASP.NET Core 3,0:

* Adicione `using Microsoft.AspNetCore.Http` à lista de diretivas using.
* Adicione [ Cookie políticas](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) para `Startup.ConfigureServices` e [use a Cookie política](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) para `Startup.Configure` :

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Adicione o cookie consentimento parcial ao arquivo *_Layout. cshtml* :

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* Adicione o arquivo *\_ Cookie ConsentPartial. cshtml* ao projeto:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Selecione a versão ASP.NET Core 2,2 deste artigo para ler sobre o cookie recurso de consentimento.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Os modelos de projeto incluem pontos de extensão e marcação fragmentado que você pode substituir pela sua política de privacidade e cookie uso.
* Um cookie recurso de consentimento permite que você solicite (e rastreie) o consentimento dos usuários para armazenar informações pessoais. Se um usuário não consentiu na coleta de dados e o aplicativo tiver [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) definido como `true` , os s não essenciais cookie não serão enviados para o navegador.
* Cookieos s podem ser marcados como essenciais. cookieOs s essenciais são enviados ao navegador mesmo quando o usuário não consentiu e o rastreamento está desabilitado.
* [TempData e Session cookie s](#tempdata) não são funcionais quando o rastreamento está desabilitado.
* A página [ Identity gerenciar](#pd) fornece um link para baixar e excluir dados do usuário.

O [aplicativo de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) permite que você teste a maioria dos pontos de extensão GDPR e as APIs adicionadas aos modelos ASP.NET Core 2,1. Consulte o arquivo [Leiame](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) para obter instruções de teste.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>ASP.NET Core suporte a GDPR no código gerado por modelo

Razor As páginas e os projetos MVC criados com os modelos de projeto incluem o seguinte suporte a GDPR:

* [ Cookie Políticas](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) e políticas [de Cookie uso](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) são definidas na `Startup` classe.
* A [exibição parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)de *\_ Cookie ConsentPartial. cshtml* . Um botão **aceitar** está incluído neste arquivo. Quando o usuário clica no botão **aceitar** , o consentimento do armazenamento cookie s é fornecido.
* A página *Pages/privacy. cshtml* ou *views/Home/privacy. cshtml* fornece uma página para detalhar a política de privacidade do seu site. O arquivo *\_ Cookie ConsentPartial. cshtml* gera um link para a página de privacidade.
* Para aplicativos criados com contas de usuário individuais, a página Gerenciar fornece links para baixar e excluir [dados de usuário pessoal](#pd).

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a>CookiePolítica de políticas e Cookie uso

As [ Cookie políticas](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) são inicializadas em `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[Usar Cookie o A política](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) é chamada em `Startup.Configure` :

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a>\_CookieExibição parcial de ConsentPartial. cshtml

A exibição parcial de *\_ Cookie ConsentPartial. cshtml* :

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Isso parcial:

* Obtém o estado do controle para o usuário. Se o aplicativo estiver configurado para exigir consentimento, o usuário deverá consentir antes que os cookie s possam ser rastreados. Se o consentimento for necessário, o cookie painel de consentimento será corrigido na parte superior da barra de navegação criada pelo arquivo *\_ layout. cshtml* .
* Fornece um `<p>` elemento HTML para resumir sua política de privacidade e cookie uso.
* Fornece um link para a página de privacidade ou a exibição em que você pode detalhar a política de privacidade do seu site.

## <a name="essential-no-loccookies"></a>cookieS essenciais

Se o consentimento para cookie o armazenamento não tiver sido fornecido, somente os cookie s marcados como essenciais serão enviados para o navegador. O código a seguir torna um cookie essencial:

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a>O provedor TempData e o estado de sessão cookie s não são essenciais

O [provedor TempData](xref:fundamentals/app-state#tempdata) cookie não é essencial. Se o rastreamento estiver desabilitado, o provedor TempData não funcionará. Para habilitar o provedor TempData quando o rastreamento estiver desabilitado, marque o TempData cookie como essencial em `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[Estado](xref:fundamentals/app-state) cookie da sessão os s não são essenciais. O estado da sessão não é funcional quando o rastreamento está desabilitado. O código a seguir torna a sessão cookie s essencial:

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Dados pessoais

ASP.NET Core aplicativos criados com contas de usuário individuais incluem código para baixar e excluir dados pessoais.

Selecione o nome de usuário e, em seguida, selecione **dados pessoais** :

![Página Gerenciar dados pessoais](gdpr/_static/pd.png)

Observações:

* Para gerar o `Account/Manage` código, consulte [Scaffold Identity ](xref:security/authentication/scaffold-identity).
* Os links de **exclusão** e **Download** só agem nos dados de identidade padrão. Aplicativos que criam dados de usuário personalizados devem ser estendidos para excluir/baixar os dados de usuário personalizados. Para obter mais informações, consulte [Adicionar, baixar e excluir dados de usuário personalizados Identity para ](xref:security/authentication/add-user-data)o.
* Os tokens salvos para o usuário que são armazenados na Identity tabela de banco de dados `AspNetUserTokens` são excluídos quando o usuário é excluído por meio do comportamento de exclusão em cascata devido à [chave estrangeira](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).
* A [autenticação de provedor externo](xref:security/authentication/social/index), como o Facebook e o Google, não está disponível antes que a cookie política seja aceita.

::: moniker-end

## <a name="encryption-at-rest"></a>Criptografia em repouso

Alguns bancos de dados e mecanismos de armazenamento permitem a criptografia em repouso. Criptografia em repouso:

* Criptografa os dados armazenados automaticamente.
* Criptografa sem configuração, programação ou outro trabalho para o software que acessa os dados.
* É a opção mais fácil e mais segura.
* Permite que o banco de dados gerencie chaves e criptografia.

Por exemplo:

* O Microsoft SQL e o Azure SQL fornecem [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).
* [O SQL Azure criptografa o banco de dados por padrão](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* Os [BLOBs, arquivos, tabelas e armazenamento de fila do Azure são criptografados por padrão](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).

Para bancos de dados que não fornecem criptografia interna em repouso, talvez você possa usar a criptografia de disco para fornecer a mesma proteção. Por exemplo:

* [BitLocker para Windows Server](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [Encfs](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>Recursos adicionais

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [GDPR-adicionando um botão revogar consentimento no ASP.NET Core](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
