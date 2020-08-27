---
title: Confirmação de conta e de recuperação de senha no ASP.NET Core
author: rick-anderson
description: Saiba como criar um aplicativo ASP.NET Core com confirmação de email e redefinição de senha.
ms.author: riande
ms.date: 03/11/2019
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
uid: security/authentication/accconfirm
ms.openlocfilehash: d6ea37ceb83ffbaa94187e0c541c79428594e4b4
ms.sourcegitcommit: 2039e60eb7b482da8298f82dcd5eda27cf747f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88906443"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="7a5d5-103">Confirmação de conta e de recuperação de senha no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a5d5-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="7a5d5-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="7a5d5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="7a5d5-105">Este tutorial mostra como criar um aplicativo ASP.NET Core com confirmação de email e redefinição de senha.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="7a5d5-106">Este tutorial **não** é um tópico inicial.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="7a5d5-107">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-107">You should be familiar with:</span></span>

* [<span data-ttu-id="7a5d5-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a5d5-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="7a5d5-109">Autenticação</span><span class="sxs-lookup"><span data-stu-id="7a5d5-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="7a5d5-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="7a5d5-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="7a5d5-111">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="7a5d5-111">Prerequisites</span></span>

[<span data-ttu-id="7a5d5-112">SDK do .NET Core 3,0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="7a5d5-112">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="7a5d5-113">Criar e testar um aplicativo Web com autenticação</span><span class="sxs-lookup"><span data-stu-id="7a5d5-113">Create and test a web app with authentication</span></span>

<span data-ttu-id="7a5d5-114">Execute os comandos a seguir para criar um aplicativo Web com autenticação.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-114">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="7a5d5-115">Execute o aplicativo, selecione o link **registrar** e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-115">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="7a5d5-116">Depois de registrado, você será redirecionado para a `/Identity/Account/RegisterConfirmation` página para, que contém um link para simular a confirmação de email:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-116">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="7a5d5-117">Selecione o link `Click here to confirm your account`.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-117">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="7a5d5-118">Selecione o link de **logon** e entre com as mesmas credenciais.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-118">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="7a5d5-119">Selecione o `Hello YourEmail@provider.com!` link, que o redireciona para a `/Identity/Account/Manage/PersonalData` página.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-119">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="7a5d5-120">Selecione a guia **dados pessoais** à esquerda e, em seguida, selecione **excluir**.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-120">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="7a5d5-121">Configurar um provedor de email</span><span class="sxs-lookup"><span data-stu-id="7a5d5-121">Configure an email provider</span></span>

<span data-ttu-id="7a5d5-122">Neste tutorial, [SendGrid](https://sendgrid.com) é usado para enviar email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-122">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="7a5d5-123">Você precisa de uma conta e chave do SendGrid para enviar email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-123">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="7a5d5-124">Você pode usar outros provedores de email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-124">You can use other email providers.</span></span> <span data-ttu-id="7a5d5-125">Recomendamos que você use o SendGrid ou outro serviço de email para enviar email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-125">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="7a5d5-126">O SMTP é difícil de proteger e configurar corretamente.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-126">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="7a5d5-127">A conta SendGrid pode exigir a [adição de um remetente](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="7a5d5-127">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="7a5d5-128">Crie uma classe para buscar a chave de email seguro.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="7a5d5-129">Para este exemplo, crie *Services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="7a5d5-130">Configurar segredos de usuário do SendGrid</span><span class="sxs-lookup"><span data-stu-id="7a5d5-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="7a5d5-131">Defina `SendGridUser` e `SendGridKey` com a [ferramenta Secret-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="7a5d5-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="7a5d5-132">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="7a5d5-133">No Windows, o Gerenciador de segredo armazena pares de chaves/valores em um *secrets.jsno* arquivo no `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` diretório.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="7a5d5-134">O conteúdo do *secrets.jsno* arquivo não está criptografado.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="7a5d5-135">A marcação a seguir mostra o *secrets.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="7a5d5-136">O `SendGridKey` valor foi removido.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="7a5d5-137">Para obter mais informações, consulte o [padrão de opções](xref:fundamentals/configuration/options) e [configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7a5d5-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="7a5d5-138">Instalar o SendGrid</span><span class="sxs-lookup"><span data-stu-id="7a5d5-138">Install SendGrid</span></span>

<span data-ttu-id="7a5d5-139">Este tutorial mostra como adicionar notificações por email por meio do [SendGrid](https://sendgrid.com/), mas você pode enviar emails usando o SMTP e outros mecanismos.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="7a5d5-140">Instale o `SendGrid` pacote NuGet:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7a5d5-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a5d5-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7a5d5-142">No console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="7a5d5-143">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="7a5d5-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7a5d5-144">No console do, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="7a5d5-145">Consulte Introdução ao [SendGrid gratuitamente](https://sendgrid.com/free/) para se registrar para uma conta gratuita do SendGrid.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="7a5d5-146">Implementar IEmailSender</span><span class="sxs-lookup"><span data-stu-id="7a5d5-146">Implement IEmailSender</span></span>

<span data-ttu-id="7a5d5-147">Para implementar `IEmailSender` , crie os *Serviços/EmailSender. cs* com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="7a5d5-148">Configurar a inicialização para o email de suporte</span><span class="sxs-lookup"><span data-stu-id="7a5d5-148">Configure startup to support email</span></span>

<span data-ttu-id="7a5d5-149">Adicione o seguinte código ao `ConfigureServices` método no arquivo *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="7a5d5-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="7a5d5-150">Adicionar `EmailSender` como um serviço transitório.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="7a5d5-151">Registre a `AuthMessageSenderOptions` instância de configuração.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a><span data-ttu-id="7a5d5-152">Scaffold RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="7a5d5-152">Scaffold RegisterConfirmation</span></span>

<span data-ttu-id="7a5d5-153">Siga as instruções para [Scaffold Identity ](xref:security/authentication/scaffold-identity) e Scaffold `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="7a5d5-153">Follow the instructions for [Scaffold Identity](xref:security/authentication/scaffold-identity) and scaffold `RegisterConfirmation`.</span></span>

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="7a5d5-154">Registrar, confirmar email e Redefinir senha</span><span class="sxs-lookup"><span data-stu-id="7a5d5-154">Register, confirm email, and reset password</span></span>

<span data-ttu-id="7a5d5-155">Execute o aplicativo Web e teste a confirmação da conta e o fluxo de recuperação de senha.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-155">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="7a5d5-156">Executar o aplicativo e registrar um novo usuário</span><span class="sxs-lookup"><span data-stu-id="7a5d5-156">Run the app and register a new user</span></span>
* <span data-ttu-id="7a5d5-157">Verifique seu email para o link de confirmação da conta.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-157">Check your email for the account confirmation link.</span></span> <span data-ttu-id="7a5d5-158">Consulte [depurar email](#debug) se você não receber o email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-158">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="7a5d5-159">Clique no link para confirmar seu email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-159">Click the link to confirm your email.</span></span>
* <span data-ttu-id="7a5d5-160">Entre com seu email e senha.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-160">Sign in with your email and password.</span></span>
* <span data-ttu-id="7a5d5-161">Saia.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-161">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="7a5d5-162">Testar redefinição de senha</span><span class="sxs-lookup"><span data-stu-id="7a5d5-162">Test password reset</span></span>

* <span data-ttu-id="7a5d5-163">Se você estiver conectado, selecione **logout**.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-163">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="7a5d5-164">Selecione o link **fazer logon** e selecione o link **esqueceu sua senha?** .</span><span class="sxs-lookup"><span data-stu-id="7a5d5-164">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="7a5d5-165">Insira o email que você usou para registrar a conta.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-165">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="7a5d5-166">Um email com um link para redefinir sua senha é enviado.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-166">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="7a5d5-167">Verifique seu email e clique no link para redefinir sua senha.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-167">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="7a5d5-168">Depois que a senha for redefinida com êxito, você poderá entrar com seu email e nova senha.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-168">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

<a name="resend"></a>

## <a name="resend-email-confirmation"></a><span data-ttu-id="7a5d5-169">Reenviar confirmação de email</span><span class="sxs-lookup"><span data-stu-id="7a5d5-169">Resend email confirmation</span></span>

<span data-ttu-id="7a5d5-170">No ASP.NET Core 5,0 e posterior, selecione o link **reenviar confirmação de email** na página de **logon** .</span><span class="sxs-lookup"><span data-stu-id="7a5d5-170">In ASP.NET Core 5.0 and later, select the **Resend email confirmation** link on the **Login** page.</span></span>

### <a name="change-email-and-activity-timeout"></a><span data-ttu-id="7a5d5-171">Alterar o tempo limite de email e atividade</span><span class="sxs-lookup"><span data-stu-id="7a5d5-171">Change email and activity timeout</span></span>

<span data-ttu-id="7a5d5-172">O tempo limite de inatividade padrão é de 14 dias.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-172">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="7a5d5-173">O código a seguir define o tempo limite de inatividade como 5 dias:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-173">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="7a5d5-174">Alterar todas as vidas de token de proteção de dados</span><span class="sxs-lookup"><span data-stu-id="7a5d5-174">Change all data protection token lifespans</span></span>

<span data-ttu-id="7a5d5-175">O código a seguir altera todos os tokens de proteção de dados período de tempo limite para 3 horas:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-175">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="7a5d5-176">Os Identity tokens de usuário internos (consulte [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) têm um [tempo limite de um dia](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="7a5d5-176">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="7a5d5-177">Alterar o ciclo de vida do token de email</span><span class="sxs-lookup"><span data-stu-id="7a5d5-177">Change the email token lifespan</span></span>

<span data-ttu-id="7a5d5-178">A vida útil do token padrão dos [ Identity tokens de usuário](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) é de [um dia](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="7a5d5-178">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="7a5d5-179">Esta seção mostra como alterar o ciclo de vida do token de email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-179">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="7a5d5-180">Adicione um [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personalizado e <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="7a5d5-180">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="7a5d5-181">Adicione o provedor personalizado ao contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-181">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="7a5d5-182">Depurar email</span><span class="sxs-lookup"><span data-stu-id="7a5d5-182">Debug email</span></span>

<span data-ttu-id="7a5d5-183">Se você não puder obter emails funcionando:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-183">If you can't get email working:</span></span>

* <span data-ttu-id="7a5d5-184">Defina um ponto de interrupção em `EmailSender.Execute` para verificar `SendGridClient.SendEmailAsync` é chamado.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-184">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="7a5d5-185">Crie um [aplicativo de console para enviar emails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) usando código semelhante para `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="7a5d5-185">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="7a5d5-186">Examine a página [atividade de email](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="7a5d5-186">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="7a5d5-187">Verifique sua pasta de spam.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-187">Check your spam folder.</span></span>
* <span data-ttu-id="7a5d5-188">Experimente outro alias de email em um provedor de email diferente (Microsoft, Yahoo, Gmail, etc.)</span><span class="sxs-lookup"><span data-stu-id="7a5d5-188">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="7a5d5-189">Tente enviar para contas de email diferentes.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-189">Try sending to different email accounts.</span></span>

<span data-ttu-id="7a5d5-190">**Uma prática recomendada de segurança** é **não** usar segredos de produção em teste e desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-190">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="7a5d5-191">Se você publicar o aplicativo no Azure, defina os segredos do SendGrid como configurações de aplicativo no portal do aplicativo Web do Azure.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-191">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="7a5d5-192">O sistema de configuração é configurado para ler chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-192">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="7a5d5-193">Combinar contas de logon sociais e locais</span><span class="sxs-lookup"><span data-stu-id="7a5d5-193">Combine social and local login accounts</span></span>

<span data-ttu-id="7a5d5-194">Para concluir esta seção, primeiro você deve habilitar um provedor de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-194">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="7a5d5-195">Consulte [Facebook, Google e autenticação de provedor externo](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="7a5d5-195">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="7a5d5-196">Você pode combinar contas locais e sociais clicando no link de email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-196">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="7a5d5-197">Na sequência a seguir, " RickAndMSFT@gmail.com " é criado primeiro como um logon local; no entanto, você pode criar a conta como um logon social primeiro e, em seguida, adicionar um logon local.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-197">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplicativo Web: RickAndMSFT@gmail.com usuário autenticado](accconfirm/_static/rick.png)

<span data-ttu-id="7a5d5-199">Clique no link **gerenciar** .</span><span class="sxs-lookup"><span data-stu-id="7a5d5-199">Click on the **Manage** link.</span></span> <span data-ttu-id="7a5d5-200">Observe o 0 externo (logons sociais) associado a essa conta.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-200">Note the 0 external (social logins) associated with this account.</span></span>

![Gerenciar modo de exibição](accconfirm/_static/manage.png)

<span data-ttu-id="7a5d5-202">Clique no link para outro serviço de logon e aceite as solicitações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-202">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="7a5d5-203">Na imagem a seguir, o Facebook é o provedor de autenticação externa:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-203">In the following image, Facebook is the external authentication provider:</span></span>

![Gerenciar seus logons externos exibir listando o Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="7a5d5-205">As duas contas foram combinadas.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-205">The two accounts have been combined.</span></span> <span data-ttu-id="7a5d5-206">Você pode entrar com qualquer uma das contas.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-206">You are able to sign in with either account.</span></span> <span data-ttu-id="7a5d5-207">Talvez você queira que os usuários adicionem contas locais caso o serviço de autenticação de logon social esteja inativo ou, provavelmente, tenha perdido o acesso à sua conta social.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-207">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="7a5d5-208">Habilitar confirmação de conta depois que um site tiver usuários</span><span class="sxs-lookup"><span data-stu-id="7a5d5-208">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="7a5d5-209">Habilitar a confirmação de conta em um site com usuários bloqueia todos os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-209">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="7a5d5-210">Os usuários existentes são bloqueados porque suas contas não são confirmadas.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-210">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="7a5d5-211">Para contornar o bloqueio de usuário existente, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-211">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="7a5d5-212">Atualize o banco de dados para marcar todos os usuários existentes como sendo confirmados.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-212">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="7a5d5-213">Confirme os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-213">Confirm existing users.</span></span> <span data-ttu-id="7a5d5-214">Por exemplo, enviar emails por lote com links de confirmação.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-214">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="7a5d5-215">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="7a5d5-215">Prerequisites</span></span>

[<span data-ttu-id="7a5d5-216">SDK do .NET Core 2,2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="7a5d5-216">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-no-locidentity"></a><span data-ttu-id="7a5d5-217">Criar um aplicativo Web e Scaffold Identity</span><span class="sxs-lookup"><span data-stu-id="7a5d5-217">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="7a5d5-218">Execute os comandos a seguir para criar um aplicativo Web com autenticação.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-218">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run
```

> [!NOTE]
> <span data-ttu-id="7a5d5-219">Se <xref:Microsoft.AspNetCore.Identity.PasswordOptions> o estiver configurado no `Startup.ConfigureServices` , a configuração de [ `[StringLength]` atributo](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) poderá ser necessária para a `Password` propriedade em Identity páginas com Scaffold.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-219">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="7a5d5-220">Uma `InputModel` `Password` propriedade é encontrada no `Areas/Identity/Pages/Account/Register.cshtml.cs` arquivo após scaffolding Identity .</span><span class="sxs-lookup"><span data-stu-id="7a5d5-220">An `InputModel` `Password` property is found in the `Areas/Identity/Pages/Account/Register.cshtml.cs` file after scaffolding Identity.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="7a5d5-221">Testar novo registro de usuário</span><span class="sxs-lookup"><span data-stu-id="7a5d5-221">Test new user registration</span></span>

<span data-ttu-id="7a5d5-222">Execute o aplicativo, selecione o link **registrar** e registre um usuário.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-222">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="7a5d5-223">Neste ponto, a única validação no email é com o [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atributo.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-223">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="7a5d5-224">Depois de enviar o registro, você está conectado ao aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-224">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="7a5d5-225">Posteriormente no tutorial, o código será atualizado para que novos usuários não possam entrar até que seu email seja validado.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-225">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="7a5d5-226">Observe que o campo da tabela `EmailConfirmed` é `False` .</span><span class="sxs-lookup"><span data-stu-id="7a5d5-226">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="7a5d5-227">Você pode querer usar esse email novamente na próxima etapa quando o aplicativo enviar um email de confirmação.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-227">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="7a5d5-228">Clique com o botão direito do mouse na linha e selecione **excluir**.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-228">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="7a5d5-229">A exclusão do alias de email torna mais fácil as etapas a seguir.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-229">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="7a5d5-230">Exigir confirmação de email</span><span class="sxs-lookup"><span data-stu-id="7a5d5-230">Require email confirmation</span></span>

<span data-ttu-id="7a5d5-231">É uma prática recomendada confirmar o email de um novo registro de usuário.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-231">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="7a5d5-232">A confirmação por email ajuda a verificar se eles não estão representando outra pessoa (ou seja, se eles não se registraram no email de outra pessoa).</span><span class="sxs-lookup"><span data-stu-id="7a5d5-232">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="7a5d5-233">Suponha que você tenha um fórum de discussão e queira impedir " yli@example.com " de se registrar como " nolivetto@contoso.com ".</span><span class="sxs-lookup"><span data-stu-id="7a5d5-233">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="7a5d5-234">Sem confirmação por email, " nolivetto@contoso.com " pode receber emails indesejados do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-234">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="7a5d5-235">Suponha que o usuário se registrou acidentalmente como " ylo@example.com " e não tenha notado a grafia incorreta de "Yli".</span><span class="sxs-lookup"><span data-stu-id="7a5d5-235">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="7a5d5-236">Eles não poderão usar a recuperação de senha porque o aplicativo não tem seu email correto.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-236">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="7a5d5-237">A confirmação por email fornece proteção limitada de bots.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-237">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="7a5d5-238">A confirmação por email não fornece proteção contra usuários mal-intencionados com muitas contas de email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-238">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="7a5d5-239">Em geral, você deseja impedir que novos usuários enviem dados para seu site antes de terem um email confirmado.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-239">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="7a5d5-240">Atualize `Startup.ConfigureServices`  para exigir um email confirmado:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-240">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="7a5d5-241">`config.SignIn.RequireConfirmedEmail = true;` impede que os usuários registrados façam logon até que seu email seja confirmado.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-241">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="7a5d5-242">Configurar provedor de email</span><span class="sxs-lookup"><span data-stu-id="7a5d5-242">Configure email provider</span></span>

<span data-ttu-id="7a5d5-243">Neste tutorial, [SendGrid](https://sendgrid.com) é usado para enviar email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-243">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="7a5d5-244">Você precisa de uma conta e chave do SendGrid para enviar email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-244">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="7a5d5-245">Você pode usar outros provedores de email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-245">You can use other email providers.</span></span> <span data-ttu-id="7a5d5-246">O ASP.NET Core 2. x inclui `System.Net.Mail` , que permite enviar emails do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-246">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="7a5d5-247">Recomendamos que você use o SendGrid ou outro serviço de email para enviar email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-247">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="7a5d5-248">O SMTP é difícil de proteger e configurar corretamente.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-248">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="7a5d5-249">Crie uma classe para buscar a chave de email seguro.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-249">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="7a5d5-250">Para este exemplo, crie *Services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-250">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="7a5d5-251">Configurar segredos de usuário do SendGrid</span><span class="sxs-lookup"><span data-stu-id="7a5d5-251">Configure SendGrid user secrets</span></span>

<span data-ttu-id="7a5d5-252">Defina `SendGridUser` e `SendGridKey` com a [ferramenta Secret-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="7a5d5-252">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="7a5d5-253">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-253">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="7a5d5-254">No Windows, o Gerenciador de segredo armazena pares de chaves/valores em um *secrets.jsno* arquivo no `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` diretório.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-254">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="7a5d5-255">O conteúdo do *secrets.jsno* arquivo não está criptografado.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-255">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="7a5d5-256">A marcação a seguir mostra o *secrets.jsno* arquivo.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-256">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="7a5d5-257">O `SendGridKey` valor foi removido.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-257">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="7a5d5-258">Para obter mais informações, consulte o [padrão de opções](xref:fundamentals/configuration/options) e [configuração](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7a5d5-258">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="7a5d5-259">Instalar o SendGrid</span><span class="sxs-lookup"><span data-stu-id="7a5d5-259">Install SendGrid</span></span>

<span data-ttu-id="7a5d5-260">Este tutorial mostra como adicionar notificações por email por meio do [SendGrid](https://sendgrid.com/), mas você pode enviar emails usando o SMTP e outros mecanismos.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-260">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="7a5d5-261">Instale o `SendGrid` pacote NuGet:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-261">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7a5d5-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7a5d5-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7a5d5-263">No console do Gerenciador de pacotes, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-263">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="7a5d5-264">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="7a5d5-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7a5d5-265">No console do, digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-265">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="7a5d5-266">Consulte Introdução ao [SendGrid gratuitamente](https://sendgrid.com/free/) para se registrar para uma conta gratuita do SendGrid.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-266">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="7a5d5-267">Implementar IEmailSender</span><span class="sxs-lookup"><span data-stu-id="7a5d5-267">Implement IEmailSender</span></span>

<span data-ttu-id="7a5d5-268">Para implementar `IEmailSender` , crie os *Serviços/EmailSender. cs* com um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-268">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="7a5d5-269">Configurar a inicialização para o email de suporte</span><span class="sxs-lookup"><span data-stu-id="7a5d5-269">Configure startup to support email</span></span>

<span data-ttu-id="7a5d5-270">Adicione o seguinte código ao `ConfigureServices` método no arquivo *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="7a5d5-270">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="7a5d5-271">Adicionar `EmailSender` como um serviço transitório.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-271">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="7a5d5-272">Registre a `AuthMessageSenderOptions` instância de configuração.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-272">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="7a5d5-273">Habilitar confirmação de conta e recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="7a5d5-273">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="7a5d5-274">O modelo tem o código para confirmação de conta e recuperação de senha.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-274">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="7a5d5-275">Localize o `OnPostAsync` método em *áreas/ Identity /pages/Account/Register.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-275">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="7a5d5-276">Impedir que usuários registrados recentemente façam logon automaticamente comentando a seguinte linha:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-276">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="7a5d5-277">O método Complete é mostrado com a linha alterada realçada:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-277">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="7a5d5-278">Registrar, confirmar email e Redefinir senha</span><span class="sxs-lookup"><span data-stu-id="7a5d5-278">Register, confirm email, and reset password</span></span>

<span data-ttu-id="7a5d5-279">Execute o aplicativo Web e teste a confirmação da conta e o fluxo de recuperação de senha.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-279">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="7a5d5-280">Executar o aplicativo e registrar um novo usuário</span><span class="sxs-lookup"><span data-stu-id="7a5d5-280">Run the app and register a new user</span></span>
* <span data-ttu-id="7a5d5-281">Verifique seu email para o link de confirmação da conta.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-281">Check your email for the account confirmation link.</span></span> <span data-ttu-id="7a5d5-282">Consulte [depurar email](#debug) se você não receber o email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-282">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="7a5d5-283">Clique no link para confirmar seu email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-283">Click the link to confirm your email.</span></span>
* <span data-ttu-id="7a5d5-284">Entre com seu email e senha.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-284">Sign in with your email and password.</span></span>
* <span data-ttu-id="7a5d5-285">Saia.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-285">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="7a5d5-286">Exibir a página Gerenciar</span><span class="sxs-lookup"><span data-stu-id="7a5d5-286">View the manage page</span></span>

<span data-ttu-id="7a5d5-287">Selecione seu nome de usuário no navegador: ![ janela do navegador com nome de usuário](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="7a5d5-287">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="7a5d5-288">A página Gerenciar é exibida com a guia **perfil** selecionada.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-288">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="7a5d5-289">O **email** mostra uma caixa de seleção indicando que o email foi confirmado.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-289">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="7a5d5-290">Testar redefinição de senha</span><span class="sxs-lookup"><span data-stu-id="7a5d5-290">Test password reset</span></span>

* <span data-ttu-id="7a5d5-291">Se você estiver conectado, selecione **logout**.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-291">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="7a5d5-292">Selecione o link **fazer logon** e selecione o link **esqueceu sua senha?** .</span><span class="sxs-lookup"><span data-stu-id="7a5d5-292">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="7a5d5-293">Insira o email que você usou para registrar a conta.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-293">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="7a5d5-294">Um email com um link para redefinir sua senha é enviado.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-294">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="7a5d5-295">Verifique seu email e clique no link para redefinir sua senha.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-295">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="7a5d5-296">Depois que a senha for redefinida com êxito, você poderá entrar com seu email e nova senha.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-296">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="7a5d5-297">Alterar o tempo limite de email e atividade</span><span class="sxs-lookup"><span data-stu-id="7a5d5-297">Change email and activity timeout</span></span>

<span data-ttu-id="7a5d5-298">O tempo limite de inatividade padrão é de 14 dias.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-298">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="7a5d5-299">O código a seguir define o tempo limite de inatividade como 5 dias:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-299">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="7a5d5-300">Alterar todas as vidas de token de proteção de dados</span><span class="sxs-lookup"><span data-stu-id="7a5d5-300">Change all data protection token lifespans</span></span>

<span data-ttu-id="7a5d5-301">O código a seguir altera todos os tokens de proteção de dados período de tempo limite para 3 horas:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-301">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="7a5d5-302">Os Identity tokens de usuário internos (consulte [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) têm um [tempo limite de um dia](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="7a5d5-302">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="7a5d5-303">Alterar o ciclo de vida do token de email</span><span class="sxs-lookup"><span data-stu-id="7a5d5-303">Change the email token lifespan</span></span>

<span data-ttu-id="7a5d5-304">A vida útil do token padrão dos [ Identity tokens de usuário](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) é de [um dia](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="7a5d5-304">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="7a5d5-305">Esta seção mostra como alterar o ciclo de vida do token de email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-305">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="7a5d5-306">Adicione um [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personalizado e <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="7a5d5-306">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="7a5d5-307">Adicione o provedor personalizado ao contêiner de serviço:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-307">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="7a5d5-308">Reenviar confirmação de email</span><span class="sxs-lookup"><span data-stu-id="7a5d5-308">Resend email confirmation</span></span>

<span data-ttu-id="7a5d5-309">Consulte [este problema do GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="7a5d5-309">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="7a5d5-310">Depurar email</span><span class="sxs-lookup"><span data-stu-id="7a5d5-310">Debug email</span></span>

<span data-ttu-id="7a5d5-311">Se você não puder obter emails funcionando:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-311">If you can't get email working:</span></span>

* <span data-ttu-id="7a5d5-312">Defina um ponto de interrupção em `EmailSender.Execute` para verificar `SendGridClient.SendEmailAsync` é chamado.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-312">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="7a5d5-313">Crie um [aplicativo de console para enviar emails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) usando código semelhante para `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="7a5d5-313">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="7a5d5-314">Examine a página [atividade de email](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="7a5d5-314">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="7a5d5-315">Verifique sua pasta de spam.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-315">Check your spam folder.</span></span>
* <span data-ttu-id="7a5d5-316">Experimente outro alias de email em um provedor de email diferente (Microsoft, Yahoo, Gmail, etc.)</span><span class="sxs-lookup"><span data-stu-id="7a5d5-316">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="7a5d5-317">Tente enviar para contas de email diferentes.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-317">Try sending to different email accounts.</span></span>

<span data-ttu-id="7a5d5-318">**Uma prática recomendada de segurança** é **não** usar segredos de produção em teste e desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-318">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="7a5d5-319">Se você publicar o aplicativo no Azure, poderá definir os segredos do SendGrid como configurações do aplicativo no portal do aplicativo Web do Azure.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-319">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="7a5d5-320">O sistema de configuração é configurado para ler chaves de variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-320">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="7a5d5-321">Combinar contas de logon sociais e locais</span><span class="sxs-lookup"><span data-stu-id="7a5d5-321">Combine social and local login accounts</span></span>

<span data-ttu-id="7a5d5-322">Para concluir esta seção, primeiro você deve habilitar um provedor de autenticação externa.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-322">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="7a5d5-323">Consulte [Facebook, Google e autenticação de provedor externo](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="7a5d5-323">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="7a5d5-324">Você pode combinar contas locais e sociais clicando no link de email.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-324">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="7a5d5-325">Na sequência a seguir, " RickAndMSFT@gmail.com " é criado primeiro como um logon local; no entanto, você pode criar a conta como um logon social primeiro e, em seguida, adicionar um logon local.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-325">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Aplicativo Web: RickAndMSFT@gmail.com usuário autenticado](accconfirm/_static/rick.png)

<span data-ttu-id="7a5d5-327">Clique no link **gerenciar** .</span><span class="sxs-lookup"><span data-stu-id="7a5d5-327">Click on the **Manage** link.</span></span> <span data-ttu-id="7a5d5-328">Observe o 0 externo (logons sociais) associado a essa conta.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-328">Note the 0 external (social logins) associated with this account.</span></span>

![Gerenciar modo de exibição](accconfirm/_static/manage.png)

<span data-ttu-id="7a5d5-330">Clique no link para outro serviço de logon e aceite as solicitações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-330">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="7a5d5-331">Na imagem a seguir, o Facebook é o provedor de autenticação externa:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-331">In the following image, Facebook is the external authentication provider:</span></span>

![Gerenciar seus logons externos exibir listando o Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="7a5d5-333">As duas contas foram combinadas.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-333">The two accounts have been combined.</span></span> <span data-ttu-id="7a5d5-334">Você pode entrar com qualquer uma das contas.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-334">You are able to sign in with either account.</span></span> <span data-ttu-id="7a5d5-335">Talvez você queira que os usuários adicionem contas locais caso o serviço de autenticação de logon social esteja inativo ou, provavelmente, tenha perdido o acesso à sua conta social.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-335">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="7a5d5-336">Habilitar confirmação de conta depois que um site tiver usuários</span><span class="sxs-lookup"><span data-stu-id="7a5d5-336">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="7a5d5-337">Habilitar a confirmação de conta em um site com usuários bloqueia todos os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-337">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="7a5d5-338">Os usuários existentes são bloqueados porque suas contas não são confirmadas.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-338">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="7a5d5-339">Para contornar o bloqueio de usuário existente, use uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="7a5d5-339">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="7a5d5-340">Atualize o banco de dados para marcar todos os usuários existentes como sendo confirmados.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-340">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="7a5d5-341">Confirme os usuários existentes.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-341">Confirm existing users.</span></span> <span data-ttu-id="7a5d5-342">Por exemplo, enviar emails por lote com links de confirmação.</span><span class="sxs-lookup"><span data-stu-id="7a5d5-342">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
