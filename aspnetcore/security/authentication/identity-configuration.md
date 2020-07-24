---
title: 'Configurar ASP.NET Core:::no-loc(Identity):::'
author: AdrienTorris
description: 'Entenda ASP.NET Core :::no-loc(Identity)::: valores padrão e saiba como configurar :::no-loc(Identity)::: Propriedades para usar valores personalizados.'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity-configuration
ms.openlocfilehash: 5c999b426742cf75b1997f5b40223e2dda112901
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160290"
---
# <a name="configure-aspnet-core-no-locidentity"></a>Configurar ASP.NET Core:::no-loc(Identity):::

ASP.NET Core :::no-loc(Identity)::: usa valores padrão para configurações como política de senha, bloqueio e configuração de cookie. Essas configurações podem ser substituídas na `Startup` classe.

## <a name="no-locidentity-options"></a>:::no-loc(Identity):::Opções

A classe [ :::no-loc(Identity)::: Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) representa as opções que podem ser usadas para configurar o :::no-loc(Identity)::: sistema. `:::no-loc(Identity):::Options`deve ser definido **após** a chamada `Add:::no-loc(Identity):::` ou `AddDefault:::no-loc(Identity):::` .

### <a name="claims-no-locidentity"></a>Declarações:::no-loc(Identity):::

[ :::no-loc(Identity)::: Options. Claims :::no-loc(Identity)::: ](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) especifica [as :::no-loc(Identity)::: Opções de declarações](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) com as propriedades mostradas na tabela a seguir.

| Propriedade | Descrição | Padrão |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | Obtém ou define o tipo de declaração usado para uma declaração de função. | [ClaimTypes. Role](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | Obtém ou define o tipo de declaração usado para a declaração de carimbo de segurança. | `AspNet.:::no-loc(Identity):::.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | Obtém ou define o tipo de declaração usado para a declaração de identificador de usuário. | [ClaimTypes. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | Obtém ou define o tipo de declaração usado para a declaração de nome de usuário. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>Bloquear

O bloqueio é definido no método [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) :

[!code-csharp[](identity-configuration/sample/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

O código anterior se baseia no `Login` :::no-loc(Identity)::: modelo. 

As opções de bloqueio são definidas em `StartUp.ConfigureServices` :

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

O código anterior define as [ :::no-loc(Identity)::: Opções](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [lockoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) com valores padrão.

Uma autenticação bem-sucedida redefine a contagem de tentativas de acesso com falha e redefine o relógio.

[ :::no-loc(Identity)::: Options. Lock](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) especifica o [lockoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) com as propriedades mostradas na tabela.

| Propriedade | Descrição | Padrão |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | Determina se um novo usuário pode ser bloqueado. | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | A quantidade de tempo que um usuário é bloqueado quando ocorre um bloqueio. | 5 minutos |
| [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | O número de tentativas de acesso com falha até que um usuário seja bloqueado, se o bloqueio estiver habilitado. | 5 |

### <a name="password"></a>Senha

Por padrão, :::no-loc(Identity)::: o requer que as senhas contenham um caractere maiúsculo, um caractere minúsculo, um dígito e um caractere não alfanumérico. As senhas devem ter pelo menos seis caracteres.

As senhas são configuradas com:

* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions>em `Startup.ConfigureServices` .
* [ `[StringLength]` atributos](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) de `Password` Propriedades se o :::no-loc(Identity)::: for [com Scaffold no aplicativo](xref:security/authentication/scaffold-identity). `InputModel``Password`as propriedades são encontradas nos seguintes arquivos:
  * `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
  * `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

[ :::no-loc(Identity)::: Opções. senha](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) especifica a [passwordoptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) com as propriedades mostradas na tabela.

| Propriedade | Descrição | Padrão |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Requer um número entre 0-9 na senha. | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | O comprimento mínimo da senha. | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Requer um caractere minúsculo na senha. | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Requer um caractere não alfanumérico na senha. | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | Aplica-se somente ao ASP.NET Core 2,0 ou posterior.<br><br> Requer o número de caracteres distintos na senha. | 1 |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Requer um caractere maiúsculo na senha. | `true` |

### <a name="sign-in"></a>Conexão

O código a seguir define `SignIn` as configurações (para valores padrão):

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

[ :::no-loc(Identity)::: Options. SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) especifica o [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) com as propriedades mostradas na tabela.

| Propriedade | Descrição | Padrão |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | Requer um email confirmado para entrar. | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | Requer um número de telefone confirmado para entrar. | `false` |

### <a name="tokens"></a>Tokens

[ :::no-loc(Identity)::: Options. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) especifica o [tokenoptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) com as propriedades mostradas na tabela.

| Propriedade | Descrição |
| -------- | ----------- |
| [AuthenticatorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider) | Obtém ou define o `AuthenticatorTokenProvider` usado para validar as entradas de dois fatores com um autenticador. |
| [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider) | Obtém ou define o `ChangeEmailTokenProvider` usado para gerar tokens usados em emails de confirmação de alteração de e-mail. |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) | Obtém ou define o `ChangePhoneNumberTokenProvider` usado para gerar tokens usados ao alterar números de telefone. |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) | Obtém ou define o provedor de token usado para gerar tokens usados em emails de confirmação de conta. |
| [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider) | Obtém ou define o [IUserTwoFactorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) usado para gerar tokens usados em emails de redefinição de senha. |
| [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap) | Usado para construir um [provedor de token de usuário](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) com a chave usada como o nome do provedor. |

### <a name="user"></a>Usuário

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[ :::no-loc(Identity)::: Options. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) especifica o [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) com as propriedades mostradas na tabela.

| Propriedade | Descrição | Padrão |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | Caracteres permitidos no nome de usuário. | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | Requer que cada usuário tenha um email exclusivo. | `false` |

### <a name="cookie-settings"></a>Configurações de cookie

Configure o cookie do aplicativo no `Startup.ConfigureServices` . [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_:::no-loc(Identity):::ServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) deve ser chamado **depois** `Add:::no-loc(Identity):::` de chamar `AddDefault:::no-loc(Identity):::` ou.

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

Para obter mais informações, consulte [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).

## <a name="password-hasher-options"></a>Opções de hash de senha

<xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions>Obtém e define opções para o hash de senha.

| Opção | Descrição |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions.CompatibilityMode> | O modo de compatibilidade usado ao aplicar o hash de novas senhas. O padrão é <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherCompatibilityMode.:::no-loc(Identity):::V3>. O primeiro byte de uma senha com hash, chamado de *marcador de formato*, especifica a versão do algoritmo de hash usado para fazer o hash da senha. Ao verificar uma senha em relação a um hash, o <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasher`1.VerifyHashedPassword*> método seleciona o algoritmo correto com base no primeiro byte. Um cliente é capaz de autenticar, independentemente de qual versão do algoritmo foi usada para fazer o hash da senha. Definir o modo de compatibilidade afeta o hash de *novas senhas*. |
| <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions.IterationCount> | O número de iterações usadas ao aplicar o hash de senhas usando PBKDF2. Esse valor é usado somente quando o <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions.CompatibilityMode> é definido como <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherCompatibilityMode.:::no-loc(Identity):::V3> . O valor deve ser um inteiro positivo e o padrão é `10000` . |

No exemplo a seguir, o <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions.IterationCount> é definido como `12000` em `Startup.ConfigureServices` :

```csharp
// using Microsoft.AspNetCore.:::no-loc(Identity):::;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```

## <a name="globally-require-all-users-to-be-authenticated"></a>Exigir globalmente que todos os usuários sejam autenticados

[!INCLUDE[](~/includes/requireAuth.md)]