---
title: Criar um aplicativo ASP.NET Core com dados de usuário protegidos por autorização
author: rick-anderson
description: 'Saiba como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização. Inclui HTTPS, autenticação, segurança, ASP.NET Core Identity .'
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: accfd46fa72c33976f8af2a39267c993447e036e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051935"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="7a089-104">Criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização</span><span class="sxs-lookup"><span data-stu-id="7a089-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="7a089-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="7a089-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="7a089-106">Consulte [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="7a089-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a089-107">Este tutorial mostra como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="7a089-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="7a089-108">Ele exibe uma lista de contatos que usuários autenticados (registrados) criaram.</span><span class="sxs-lookup"><span data-stu-id="7a089-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="7a089-109">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="7a089-109">There are three security groups:</span></span>

* <span data-ttu-id="7a089-110">**Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="7a089-111">**Os gerentes** podem aprovar ou rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="7a089-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="7a089-112">Somente contatos aprovados são visíveis para os usuários.</span><span class="sxs-lookup"><span data-stu-id="7a089-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="7a089-113">**Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="7a089-114">As imagens neste documento não correspondem exatamente aos modelos mais recentes.</span><span class="sxs-lookup"><span data-stu-id="7a089-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="7a089-115">Na imagem a seguir, o usuário Rick ( `rick@example.com` ) está conectado.</span><span class="sxs-lookup"><span data-stu-id="7a089-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="7a089-116">Rick só pode exibir contatos aprovados e **Editar** / **excluir** / **criar novos** links para seus contatos.</span><span class="sxs-lookup"><span data-stu-id="7a089-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="7a089-117">Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** .</span><span class="sxs-lookup"><span data-stu-id="7a089-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="7a089-118">Outros usuários não verão o último registro até que um gerente ou Administrador altere o status para "aprovado".</span><span class="sxs-lookup"><span data-stu-id="7a089-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="7a089-120">Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:</span><span class="sxs-lookup"><span data-stu-id="7a089-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de tela mostrando a manager@contoso.com entrada](secure-data/_static/manager1.png)

<span data-ttu-id="7a089-122">A imagem a seguir mostra a exibição de detalhes dos gerentes de um contato:</span><span class="sxs-lookup"><span data-stu-id="7a089-122">The following image shows the managers details view of a contact:</span></span>

![Exibição do gerente de um contato](secure-data/_static/manager.png)

<span data-ttu-id="7a089-124">Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.</span><span class="sxs-lookup"><span data-stu-id="7a089-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="7a089-125">Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:</span><span class="sxs-lookup"><span data-stu-id="7a089-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de tela mostrando a admin@contoso.com entrada](secure-data/_static/admin.png)

<span data-ttu-id="7a089-127">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="7a089-127">The administrator has all privileges.</span></span> <span data-ttu-id="7a089-128">Ela pode ler/editar/excluir qualquer contato e alterar o status dos contatos.</span><span class="sxs-lookup"><span data-stu-id="7a089-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="7a089-129">O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="7a089-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="7a089-130">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="7a089-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="7a089-131">`ContactIsOwnerAuthorizationHandler`: Garante que um usuário só possa editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="7a089-132">`ContactManagerAuthorizationHandler`: Permite que os gerentes aprovem ou rejeitem contatos.</span><span class="sxs-lookup"><span data-stu-id="7a089-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="7a089-133">`ContactAdministratorsAuthorizationHandler`: Permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.</span><span class="sxs-lookup"><span data-stu-id="7a089-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7a089-134">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="7a089-134">Prerequisites</span></span>

<span data-ttu-id="7a089-135">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="7a089-135">This tutorial is advanced.</span></span> <span data-ttu-id="7a089-136">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="7a089-136">You should be familiar with:</span></span>

* [<span data-ttu-id="7a089-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a089-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="7a089-138">Autenticação</span><span class="sxs-lookup"><span data-stu-id="7a089-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="7a089-139">Confirmação de conta e recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="7a089-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="7a089-140">Autorização</span><span class="sxs-lookup"><span data-stu-id="7a089-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="7a089-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="7a089-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="7a089-142">O aplicativo iniciador e concluído</span><span class="sxs-lookup"><span data-stu-id="7a089-142">The starter and completed app</span></span>

<span data-ttu-id="7a089-143">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="7a089-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="7a089-144">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="7a089-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="7a089-145">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="7a089-145">The starter app</span></span>

<span data-ttu-id="7a089-146">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="7a089-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="7a089-147">Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="7a089-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="7a089-148">Proteger dados do usuário</span><span class="sxs-lookup"><span data-stu-id="7a089-148">Secure user data</span></span>

<span data-ttu-id="7a089-149">As seções a seguir têm todas as principais etapas para criar o aplicativo de dados de usuário seguro.</span><span class="sxs-lookup"><span data-stu-id="7a089-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="7a089-150">Talvez você ache útil fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="7a089-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="7a089-151">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="7a089-151">Tie the contact data to the user</span></span>

<span data-ttu-id="7a089-152">Use a [Identity](xref:security/authentication/identity) ID de usuário ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="7a089-152">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="7a089-153">Adicione `OwnerID` e `ContactStatus` ao `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="7a089-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="7a089-154">`OwnerID` é a ID do usuário da `AspNetUser` tabela no banco de [Identity](xref:security/authentication/identity) dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="7a089-155">O `Status` campo determina se um contato é visível por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="7a089-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="7a089-156">Crie uma nova migração e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="7a089-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="7a089-157">Adicionar serviços de função a Identity</span><span class="sxs-lookup"><span data-stu-id="7a089-157">Add Role services to Identity</span></span>

<span data-ttu-id="7a089-158">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="7a089-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="7a089-159">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="7a089-159">Require authenticated users</span></span>

<span data-ttu-id="7a089-160">Defina a política de autenticação de fallback para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="7a089-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="7a089-161">O código realçado anterior define a [política de autenticação de fallback](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="7a089-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="7a089-162">A política de autenticação de fallback exige que \* *_todos_* os usuários sejam autenticados, exceto por Razor páginas, controladores ou métodos de ação com um atributo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="7a089-162">The fallback authentication policy requires \* *_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="7a089-163">Por exemplo, Razor páginas, controladores ou métodos de ação com `[AllowAnonymous]` ou `[Authorize(PolicyName="MyPolicy")]` usam o atributo de autenticação aplicado em vez da política de autenticação de fallback.</span><span class="sxs-lookup"><span data-stu-id="7a089-163">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="7a089-164">A política de autenticação de fallback:</span><span class="sxs-lookup"><span data-stu-id="7a089-164">The fallback authentication policy:</span></span>

<span data-ttu-id="7a089-165">_ É aplicado a todas as solicitações que não especificam explicitamente uma política de autenticação.</span><span class="sxs-lookup"><span data-stu-id="7a089-165">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="7a089-166">Para solicitações servidas pelo roteamento de ponto de extremidade, isso inclui qualquer ponto de extremidade que não especifica um atributo de autorização.</span><span class="sxs-lookup"><span data-stu-id="7a089-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="7a089-167">Para solicitações servidas por outro middleware após o middleware de autorização, como [arquivos estáticos](xref:fundamentals/static-files), isso aplicaria a política a todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="7a089-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="7a089-168">Definir a política de autenticação de fallback para exigir que os usuários sejam autenticados protege Razor páginas e controladores adicionados recentemente.</span><span class="sxs-lookup"><span data-stu-id="7a089-168">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="7a089-169">Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor páginas para incluir o `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="7a089-169">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="7a089-170">A <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> classe também contém <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="7a089-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="7a089-171">O `DefaultPolicy` é a política usada com o `[Authorize]` atributo quando nenhuma política é especificada.</span><span class="sxs-lookup"><span data-stu-id="7a089-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="7a089-172">`[Authorize]` Não contém uma política nomeada, ao contrário de `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="7a089-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="7a089-173">Para obter mais informações sobre políticas, consulte <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="7a089-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="7a089-174">Uma maneira alternativa para os controladores e Razor páginas do MVC exigir que todos os usuários sejam autenticados é adicionar um filtro de autorização:</span><span class="sxs-lookup"><span data-stu-id="7a089-174">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="7a089-175">O código anterior usa um filtro de autorização, a definição da política de fallback usa o roteamento de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="7a089-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="7a089-176">Definir a política de fallback é a maneira preferida de exigir que todos os usuários sejam autenticados.</span><span class="sxs-lookup"><span data-stu-id="7a089-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="7a089-177">Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às `Index` páginas e `Privacy` para que os usuários anônimos possam obter informações sobre o site antes de se registrarem:</span><span class="sxs-lookup"><span data-stu-id="7a089-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="7a089-178">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="7a089-178">Configure the test account</span></span>

<span data-ttu-id="7a089-179">A `SeedData` classe cria duas contas: administrador e Gerenciador.</span><span class="sxs-lookup"><span data-stu-id="7a089-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="7a089-180">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="7a089-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="7a089-181">Defina a senha do diretório do projeto (o diretório que contém *Program.cs* ):</span><span class="sxs-lookup"><span data-stu-id="7a089-181">Set the password from the project directory (the directory containing *Program.cs* ):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="7a089-182">Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.</span><span class="sxs-lookup"><span data-stu-id="7a089-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="7a089-183">Atualize `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="7a089-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="7a089-184">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="7a089-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="7a089-185">Atualize o `Initialize` método na `SeedData` classe para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="7a089-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="7a089-186">Adicione a ID de usuário do administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="7a089-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="7a089-187">Faça um dos contatos "enviado" e um "rejeitado".</span><span class="sxs-lookup"><span data-stu-id="7a089-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="7a089-188">Adicione a ID de usuário e o status a todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="7a089-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="7a089-189">Apenas um contato é mostrado:</span><span class="sxs-lookup"><span data-stu-id="7a089-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="7a089-190">Criar manipuladores de autorização de administrador, gerente e proprietário</span><span class="sxs-lookup"><span data-stu-id="7a089-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="7a089-191">Crie uma `ContactIsOwnerAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="7a089-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="7a089-192">O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="7a089-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="7a089-193">O `ContactIsOwnerAuthorizationHandler` contexto de chamadas [. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="7a089-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="7a089-194">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="7a089-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="7a089-195">Retornar `context.Succeed` quando os requisitos forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="7a089-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="7a089-196">Retornar `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="7a089-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="7a089-197">`Task.CompletedTask` Não é êxito ou falha &mdash; permite que outros manipuladores de autorização sejam executados.</span><span class="sxs-lookup"><span data-stu-id="7a089-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="7a089-198">Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="7a089-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="7a089-199">O aplicativo permite aos proprietários de contato editar/Excluir/criar seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="7a089-200">`ContactIsOwnerAuthorizationHandler` Não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="7a089-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="7a089-201">Criar um manipulador de autorização de gerente</span><span class="sxs-lookup"><span data-stu-id="7a089-201">Create a manager authorization handler</span></span>

<span data-ttu-id="7a089-202">Crie uma `ContactManagerAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="7a089-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="7a089-203">O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="7a089-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="7a089-204">Somente os gerentes podem aprovar ou rejeitar alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="7a089-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="7a089-205">Criar um manipulador de autorização de administrador</span><span class="sxs-lookup"><span data-stu-id="7a089-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="7a089-206">Crie uma `ContactAdministratorsAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="7a089-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="7a089-207">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="7a089-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="7a089-208">O administrador pode realizar todas as operações.</span><span class="sxs-lookup"><span data-stu-id="7a089-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="7a089-209">Registrar os manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="7a089-209">Register the authorization handlers</span></span>

<span data-ttu-id="7a089-210">Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="7a089-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="7a089-211">O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [Identity](xref:security/authentication/identity) , que se baseia em Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="7a089-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="7a089-212">Registre os manipuladores com a coleção de serviços para que fiquem disponíveis para o `ContactsController` através de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7a089-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7a089-213">Adicione o seguinte código ao final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7a089-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="7a089-214">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="7a089-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="7a089-215">Eles são singletons porque não usam o EF e todas as informações necessárias estão no `Context` parâmetro do `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="7a089-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="7a089-216">Autorização de suporte</span><span class="sxs-lookup"><span data-stu-id="7a089-216">Support authorization</span></span>

<span data-ttu-id="7a089-217">Nesta seção, você atualizará as Razor páginas e adicionará uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="7a089-217">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="7a089-218">Examinar a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="7a089-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="7a089-219">Examine a `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="7a089-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="7a089-220">Essa classe contém os requisitos aos quais o aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="7a089-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="7a089-221">Criar uma classe base para as páginas de contatos Razor</span><span class="sxs-lookup"><span data-stu-id="7a089-221">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="7a089-222">Crie uma classe base que contenha os serviços usados nas páginas de contatos Razor .</span><span class="sxs-lookup"><span data-stu-id="7a089-222">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="7a089-223">A classe base coloca o código de inicialização em um local:</span><span class="sxs-lookup"><span data-stu-id="7a089-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="7a089-224">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="7a089-224">The preceding code:</span></span>

* <span data-ttu-id="7a089-225">Adiciona o `IAuthorizationService` serviço para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="7a089-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="7a089-226">Adiciona o Identity `UserManager` serviço.</span><span class="sxs-lookup"><span data-stu-id="7a089-226">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="7a089-227">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="7a089-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="7a089-228">Atualizar o CREATEMODEL</span><span class="sxs-lookup"><span data-stu-id="7a089-228">Update the CreateModel</span></span>

<span data-ttu-id="7a089-229">Atualize o Construtor criar modelo de página para usar a `DI_BasePageModel` classe base:</span><span class="sxs-lookup"><span data-stu-id="7a089-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="7a089-230">Atualize o `CreateModel.OnPostAsync` método para:</span><span class="sxs-lookup"><span data-stu-id="7a089-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="7a089-231">Adicione a ID de usuário ao `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="7a089-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="7a089-232">Chame o manipulador de autorização para verificar se o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="7a089-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="7a089-233">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="7a089-233">Update the IndexModel</span></span>

<span data-ttu-id="7a089-234">Atualize o `OnGetAsync` método para que somente os contatos aprovados sejam mostrados para usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="7a089-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="7a089-235">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="7a089-235">Update the EditModel</span></span>

<span data-ttu-id="7a089-236">Adicione um manipulador de autorização para verificar se o usuário possui o contato.</span><span class="sxs-lookup"><span data-stu-id="7a089-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="7a089-237">Como a autorização de recursos está sendo validada, o `[Authorize]` atributo não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="7a089-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="7a089-238">O aplicativo não tem acesso ao recurso quando os atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="7a089-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="7a089-239">A autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="7a089-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="7a089-240">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, seja carregando-o no modelo de página ou carregando-o dentro do próprio manipulador.</span><span class="sxs-lookup"><span data-stu-id="7a089-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="7a089-241">Você acessa com frequência o recurso passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="7a089-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="7a089-242">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="7a089-242">Update the DeleteModel</span></span>

<span data-ttu-id="7a089-243">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se o usuário tem a permissão Excluir no contato.</span><span class="sxs-lookup"><span data-stu-id="7a089-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="7a089-244">Injetar o serviço de autorização nas exibições</span><span class="sxs-lookup"><span data-stu-id="7a089-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="7a089-245">Atualmente, a interface do usuário mostra links de edição e exclusão de contatos que o usuário não pode modificar.</span><span class="sxs-lookup"><span data-stu-id="7a089-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="7a089-246">Insira o serviço de autorização no arquivo *pages/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="7a089-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="7a089-247">A marcação anterior adiciona várias `using` instruções.</span><span class="sxs-lookup"><span data-stu-id="7a089-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="7a089-248">Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="7a089-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="7a089-249">Ocultar links de usuários que não têm permissão para alterar dados não protege o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7a089-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="7a089-250">Ocultar links torna o aplicativo mais amigável exibindo apenas links válidos.</span><span class="sxs-lookup"><span data-stu-id="7a089-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="7a089-251">Os usuários podem invadir as URLs geradas para invocar operações de edição e exclusão nos dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="7a089-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="7a089-252">A Razor página ou o controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-252">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="7a089-253">Atualizar detalhes</span><span class="sxs-lookup"><span data-stu-id="7a089-253">Update Details</span></span>

<span data-ttu-id="7a089-254">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="7a089-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="7a089-255">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="7a089-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="7a089-256">Adicionar ou remover um usuário de uma função</span><span class="sxs-lookup"><span data-stu-id="7a089-256">Add or remove a user to a role</span></span>

<span data-ttu-id="7a089-257">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="7a089-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="7a089-258">Removendo privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="7a089-258">Removing privileges from a user.</span></span> <span data-ttu-id="7a089-259">Por exemplo, ativar mudo de um usuário em um aplicativo de chat.</span><span class="sxs-lookup"><span data-stu-id="7a089-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="7a089-260">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="7a089-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="7a089-261">Diferenças entre desafio e proíba</span><span class="sxs-lookup"><span data-stu-id="7a089-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="7a089-262">Esse aplicativo define a política padrão para [exigir usuários autenticados](#rau).</span><span class="sxs-lookup"><span data-stu-id="7a089-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="7a089-263">O código a seguir permite usuários anônimos.</span><span class="sxs-lookup"><span data-stu-id="7a089-263">The following code allows anonymous users.</span></span> <span data-ttu-id="7a089-264">Os usuários anônimos têm permissão para mostrar as diferenças entre o desafio versus proíba.</span><span class="sxs-lookup"><span data-stu-id="7a089-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="7a089-265">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="7a089-265">In the preceding code:</span></span>

* <span data-ttu-id="7a089-266">Quando o usuário **não** é autenticado, um `ChallengeResult` é retornado.</span><span class="sxs-lookup"><span data-stu-id="7a089-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="7a089-267">Quando um `ChallengeResult` é retornado, o usuário é redirecionado para a página de entrada.</span><span class="sxs-lookup"><span data-stu-id="7a089-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="7a089-268">Quando o usuário é autenticado, mas não autorizado, um `ForbidResult` é retornado.</span><span class="sxs-lookup"><span data-stu-id="7a089-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="7a089-269">Quando um `ForbidResult` é retornado, o usuário é redirecionado para a página acesso negado.</span><span class="sxs-lookup"><span data-stu-id="7a089-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="7a089-270">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="7a089-270">Test the completed app</span></span>

<span data-ttu-id="7a089-271">Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="7a089-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="7a089-272">Escolha uma senha forte: Use oito ou mais caracteres e, pelo menos, um caractere, número e símbolo em letras maiúsculas.</span><span class="sxs-lookup"><span data-stu-id="7a089-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="7a089-273">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="7a089-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="7a089-274">Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="7a089-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="7a089-275">Se o aplicativo tiver contatos:</span><span class="sxs-lookup"><span data-stu-id="7a089-275">If the app has contacts:</span></span>

* <span data-ttu-id="7a089-276">Exclua todos os registros na `Contact` tabela.</span><span class="sxs-lookup"><span data-stu-id="7a089-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="7a089-277">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="7a089-278">Uma maneira fácil de testar o aplicativo concluído é iniciar três navegadores diferentes (ou sessões Incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="7a089-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="7a089-279">Em um navegador, registre um novo usuário (por exemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="7a089-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="7a089-280">Entre em cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="7a089-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="7a089-281">Verifique as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="7a089-281">Verify the following operations:</span></span>

* <span data-ttu-id="7a089-282">Os usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="7a089-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="7a089-283">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="7a089-284">Os gerentes podem aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="7a089-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="7a089-285">A `Details` exibição mostra os botões **aprovar** e **rejeitar** .</span><span class="sxs-lookup"><span data-stu-id="7a089-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="7a089-286">Os administradores podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="7a089-287">Usuário</span><span class="sxs-lookup"><span data-stu-id="7a089-287">User</span></span>                | <span data-ttu-id="7a089-288">Propagado pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="7a089-288">Seeded by the app</span></span> | <span data-ttu-id="7a089-289">Opções</span><span class="sxs-lookup"><span data-stu-id="7a089-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="7a089-290">No</span><span class="sxs-lookup"><span data-stu-id="7a089-290">No</span></span>                | <span data-ttu-id="7a089-291">Edite/exclua os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="7a089-292">Yes</span><span class="sxs-lookup"><span data-stu-id="7a089-292">Yes</span></span>               | <span data-ttu-id="7a089-293">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="7a089-294">Yes</span><span class="sxs-lookup"><span data-stu-id="7a089-294">Yes</span></span>               | <span data-ttu-id="7a089-295">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="7a089-296">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="7a089-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="7a089-297">Copie a URL para excluir e editar do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="7a089-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="7a089-298">Cole esses links no navegador do usuário de teste para verificar se o usuário de teste não pode executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="7a089-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="7a089-299">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="7a089-299">Create the starter app</span></span>

* <span data-ttu-id="7a089-300">Criar um Razor aplicativo de páginas chamado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="7a089-300">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="7a089-301">Crie o aplicativo com **contas de usuário individuais** .</span><span class="sxs-lookup"><span data-stu-id="7a089-301">Create the app with **Individual User Accounts** .</span></span>
  * <span data-ttu-id="7a089-302">Nomeie-o como "ContactManager" para que o namespace corresponda ao namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="7a089-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="7a089-303">`-uld` Especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="7a089-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="7a089-304">Adicionar *modelos/Contact. cs* :</span><span class="sxs-lookup"><span data-stu-id="7a089-304">Add *Models/Contact.cs* :</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="7a089-305">Scaffold o `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="7a089-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="7a089-306">Crie uma migração inicial e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="7a089-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="7a089-307">Se você tiver um bug com o `dotnet aspnet-codegenerator razorpage` comando, consulte [este problema do GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="7a089-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="7a089-308">Atualize a âncora **ContactManager** no arquivo *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="7a089-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="7a089-309">Testar o aplicativo Criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="7a089-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="7a089-310">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="7a089-310">Seed the database</span></span>

<span data-ttu-id="7a089-311">Adicione a classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) à pasta de *dados* :</span><span class="sxs-lookup"><span data-stu-id="7a089-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="7a089-312">Chamada `SeedData.Initialize` de `Main` :</span><span class="sxs-lookup"><span data-stu-id="7a089-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="7a089-313">Teste se o aplicativo propagau o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-313">Test that the app seeded the database.</span></span> <span data-ttu-id="7a089-314">Se houver linhas no BD de contato, o método de semente não será executado.</span><span class="sxs-lookup"><span data-stu-id="7a089-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="7a089-315">Este tutorial mostra como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="7a089-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="7a089-316">Ele exibe uma lista de contatos que usuários autenticados (registrados) criaram.</span><span class="sxs-lookup"><span data-stu-id="7a089-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="7a089-317">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="7a089-317">There are three security groups:</span></span>

* <span data-ttu-id="7a089-318">**Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="7a089-319">**Os gerentes** podem aprovar ou rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="7a089-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="7a089-320">Somente contatos aprovados são visíveis para os usuários.</span><span class="sxs-lookup"><span data-stu-id="7a089-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="7a089-321">**Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="7a089-322">Na imagem a seguir, o usuário Rick ( `rick@example.com` ) está conectado.</span><span class="sxs-lookup"><span data-stu-id="7a089-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="7a089-323">Rick só pode exibir contatos aprovados e **Editar** / **excluir** / **criar novos** links para seus contatos.</span><span class="sxs-lookup"><span data-stu-id="7a089-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="7a089-324">Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** .</span><span class="sxs-lookup"><span data-stu-id="7a089-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="7a089-325">Outros usuários não verão o último registro até que um gerente ou Administrador altere o status para "aprovado".</span><span class="sxs-lookup"><span data-stu-id="7a089-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="7a089-327">Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:</span><span class="sxs-lookup"><span data-stu-id="7a089-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de tela mostrando a manager@contoso.com entrada](secure-data/_static/manager1.png)

<span data-ttu-id="7a089-329">A imagem a seguir mostra a exibição de detalhes dos gerentes de um contato:</span><span class="sxs-lookup"><span data-stu-id="7a089-329">The following image shows the managers details view of a contact:</span></span>

![Exibição do gerente de um contato](secure-data/_static/manager.png)

<span data-ttu-id="7a089-331">Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.</span><span class="sxs-lookup"><span data-stu-id="7a089-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="7a089-332">Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:</span><span class="sxs-lookup"><span data-stu-id="7a089-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de tela mostrando a admin@contoso.com entrada](secure-data/_static/admin.png)

<span data-ttu-id="7a089-334">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="7a089-334">The administrator has all privileges.</span></span> <span data-ttu-id="7a089-335">Ela pode ler/editar/excluir qualquer contato e alterar o status dos contatos.</span><span class="sxs-lookup"><span data-stu-id="7a089-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="7a089-336">O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="7a089-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="7a089-337">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="7a089-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="7a089-338">`ContactIsOwnerAuthorizationHandler`: Garante que um usuário só possa editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="7a089-339">`ContactManagerAuthorizationHandler`: Permite que os gerentes aprovem ou rejeitem contatos.</span><span class="sxs-lookup"><span data-stu-id="7a089-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="7a089-340">`ContactAdministratorsAuthorizationHandler`: Permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.</span><span class="sxs-lookup"><span data-stu-id="7a089-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7a089-341">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="7a089-341">Prerequisites</span></span>

<span data-ttu-id="7a089-342">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="7a089-342">This tutorial is advanced.</span></span> <span data-ttu-id="7a089-343">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="7a089-343">You should be familiar with:</span></span>

* [<span data-ttu-id="7a089-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a089-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="7a089-345">Autenticação</span><span class="sxs-lookup"><span data-stu-id="7a089-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="7a089-346">Confirmação de conta e recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="7a089-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="7a089-347">Autorização</span><span class="sxs-lookup"><span data-stu-id="7a089-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="7a089-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="7a089-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="7a089-349">O aplicativo iniciador e concluído</span><span class="sxs-lookup"><span data-stu-id="7a089-349">The starter and completed app</span></span>

<span data-ttu-id="7a089-350">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="7a089-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="7a089-351">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="7a089-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="7a089-352">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="7a089-352">The starter app</span></span>

<span data-ttu-id="7a089-353">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="7a089-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="7a089-354">Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="7a089-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="7a089-355">Proteger dados do usuário</span><span class="sxs-lookup"><span data-stu-id="7a089-355">Secure user data</span></span>

<span data-ttu-id="7a089-356">As seções a seguir têm todas as principais etapas para criar o aplicativo de dados de usuário seguro.</span><span class="sxs-lookup"><span data-stu-id="7a089-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="7a089-357">Talvez você ache útil fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="7a089-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="7a089-358">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="7a089-358">Tie the contact data to the user</span></span>

<span data-ttu-id="7a089-359">Use a [Identity](xref:security/authentication/identity) ID de usuário ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="7a089-359">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="7a089-360">Adicione `OwnerID` e `ContactStatus` ao `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="7a089-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="7a089-361">`OwnerID` é a ID do usuário da `AspNetUser` tabela no banco de [Identity](xref:security/authentication/identity) dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="7a089-362">O `Status` campo determina se um contato é visível por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="7a089-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="7a089-363">Crie uma nova migração e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="7a089-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="7a089-364">Adicionar serviços de função a Identity</span><span class="sxs-lookup"><span data-stu-id="7a089-364">Add Role services to Identity</span></span>

<span data-ttu-id="7a089-365">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="7a089-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="7a089-366">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="7a089-366">Require authenticated users</span></span>

<span data-ttu-id="7a089-367">Defina a política de autenticação padrão para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="7a089-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="7a089-368">Você pode recusar a autenticação na Razor página, controlador ou nível de método de ação com o `[AllowAnonymous]` atributo.</span><span class="sxs-lookup"><span data-stu-id="7a089-368">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="7a089-369">Definir a política de autenticação padrão para exigir que os usuários sejam autenticados protege Razor páginas e controladores adicionados recentemente.</span><span class="sxs-lookup"><span data-stu-id="7a089-369">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="7a089-370">Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor páginas para incluir o `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="7a089-370">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="7a089-371">Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às páginas de índice, sobre e de contato para que os usuários anônimos possam obter informações sobre o site antes de se registrarem.</span><span class="sxs-lookup"><span data-stu-id="7a089-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="7a089-372">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="7a089-372">Configure the test account</span></span>

<span data-ttu-id="7a089-373">A `SeedData` classe cria duas contas: administrador e Gerenciador.</span><span class="sxs-lookup"><span data-stu-id="7a089-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="7a089-374">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="7a089-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="7a089-375">Defina a senha do diretório do projeto (o diretório que contém *Program.cs* ):</span><span class="sxs-lookup"><span data-stu-id="7a089-375">Set the password from the project directory (the directory containing *Program.cs* ):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="7a089-376">Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.</span><span class="sxs-lookup"><span data-stu-id="7a089-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="7a089-377">Atualize `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="7a089-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="7a089-378">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="7a089-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="7a089-379">Atualize o `Initialize` método na `SeedData` classe para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="7a089-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="7a089-380">Adicione a ID de usuário do administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="7a089-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="7a089-381">Faça um dos contatos "enviado" e um "rejeitado".</span><span class="sxs-lookup"><span data-stu-id="7a089-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="7a089-382">Adicione a ID de usuário e o status a todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="7a089-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="7a089-383">Apenas um contato é mostrado:</span><span class="sxs-lookup"><span data-stu-id="7a089-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="7a089-384">Criar manipuladores de autorização de administrador, gerente e proprietário</span><span class="sxs-lookup"><span data-stu-id="7a089-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="7a089-385">Crie uma pasta de *autorização* e crie uma `ContactIsOwnerAuthorizationHandler` classe nela.</span><span class="sxs-lookup"><span data-stu-id="7a089-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="7a089-386">O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="7a089-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="7a089-387">O `ContactIsOwnerAuthorizationHandler` contexto de chamadas [. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="7a089-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="7a089-388">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="7a089-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="7a089-389">Retornar `context.Succeed` quando os requisitos forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="7a089-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="7a089-390">Retornar `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="7a089-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="7a089-391">`Task.CompletedTask` Não é êxito ou falha &mdash; permite que outros manipuladores de autorização sejam executados.</span><span class="sxs-lookup"><span data-stu-id="7a089-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="7a089-392">Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="7a089-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="7a089-393">O aplicativo permite aos proprietários de contato editar/Excluir/criar seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="7a089-394">`ContactIsOwnerAuthorizationHandler` Não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="7a089-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="7a089-395">Criar um manipulador de autorização de gerente</span><span class="sxs-lookup"><span data-stu-id="7a089-395">Create a manager authorization handler</span></span>

<span data-ttu-id="7a089-396">Crie uma `ContactManagerAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="7a089-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="7a089-397">O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="7a089-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="7a089-398">Somente os gerentes podem aprovar ou rejeitar alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="7a089-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="7a089-399">Criar um manipulador de autorização de administrador</span><span class="sxs-lookup"><span data-stu-id="7a089-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="7a089-400">Crie uma `ContactAdministratorsAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="7a089-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="7a089-401">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="7a089-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="7a089-402">O administrador pode realizar todas as operações.</span><span class="sxs-lookup"><span data-stu-id="7a089-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="7a089-403">Registrar os manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="7a089-403">Register the authorization handlers</span></span>

<span data-ttu-id="7a089-404">Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="7a089-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="7a089-405">O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [Identity](xref:security/authentication/identity) , que se baseia em Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="7a089-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="7a089-406">Registre os manipuladores com a coleção de serviços para que fiquem disponíveis para o `ContactsController` através de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7a089-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7a089-407">Adicione o seguinte código ao final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7a089-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="7a089-408">`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="7a089-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="7a089-409">Eles são singletons porque não usam o EF e todas as informações necessárias estão no `Context` parâmetro do `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="7a089-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="7a089-410">Autorização de suporte</span><span class="sxs-lookup"><span data-stu-id="7a089-410">Support authorization</span></span>

<span data-ttu-id="7a089-411">Nesta seção, você atualizará as Razor páginas e adicionará uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="7a089-411">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="7a089-412">Examinar a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="7a089-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="7a089-413">Examine a `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="7a089-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="7a089-414">Essa classe contém os requisitos aos quais o aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="7a089-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="7a089-415">Criar uma classe base para as páginas de contatos Razor</span><span class="sxs-lookup"><span data-stu-id="7a089-415">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="7a089-416">Crie uma classe base que contenha os serviços usados nas páginas de contatos Razor .</span><span class="sxs-lookup"><span data-stu-id="7a089-416">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="7a089-417">A classe base coloca o código de inicialização em um local:</span><span class="sxs-lookup"><span data-stu-id="7a089-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="7a089-418">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="7a089-418">The preceding code:</span></span>

* <span data-ttu-id="7a089-419">Adiciona o `IAuthorizationService` serviço para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="7a089-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="7a089-420">Adiciona o Identity `UserManager` serviço.</span><span class="sxs-lookup"><span data-stu-id="7a089-420">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="7a089-421">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="7a089-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="7a089-422">Atualizar o CREATEMODEL</span><span class="sxs-lookup"><span data-stu-id="7a089-422">Update the CreateModel</span></span>

<span data-ttu-id="7a089-423">Atualize o Construtor criar modelo de página para usar a `DI_BasePageModel` classe base:</span><span class="sxs-lookup"><span data-stu-id="7a089-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="7a089-424">Atualize o `CreateModel.OnPostAsync` método para:</span><span class="sxs-lookup"><span data-stu-id="7a089-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="7a089-425">Adicione a ID de usuário ao `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="7a089-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="7a089-426">Chame o manipulador de autorização para verificar se o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="7a089-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="7a089-427">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="7a089-427">Update the IndexModel</span></span>

<span data-ttu-id="7a089-428">Atualize o `OnGetAsync` método para que somente os contatos aprovados sejam mostrados para usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="7a089-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="7a089-429">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="7a089-429">Update the EditModel</span></span>

<span data-ttu-id="7a089-430">Adicione um manipulador de autorização para verificar se o usuário possui o contato.</span><span class="sxs-lookup"><span data-stu-id="7a089-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="7a089-431">Como a autorização de recursos está sendo validada, o `[Authorize]` atributo não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="7a089-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="7a089-432">O aplicativo não tem acesso ao recurso quando os atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="7a089-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="7a089-433">A autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="7a089-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="7a089-434">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, seja carregando-o no modelo de página ou carregando-o dentro do próprio manipulador.</span><span class="sxs-lookup"><span data-stu-id="7a089-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="7a089-435">Você acessa com frequência o recurso passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="7a089-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="7a089-436">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="7a089-436">Update the DeleteModel</span></span>

<span data-ttu-id="7a089-437">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se o usuário tem a permissão Excluir no contato.</span><span class="sxs-lookup"><span data-stu-id="7a089-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="7a089-438">Injetar o serviço de autorização nas exibições</span><span class="sxs-lookup"><span data-stu-id="7a089-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="7a089-439">Atualmente, a interface do usuário mostra links de edição e exclusão de contatos que o usuário não pode modificar.</span><span class="sxs-lookup"><span data-stu-id="7a089-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="7a089-440">Insira o serviço de autorização no arquivo *views/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="7a089-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="7a089-441">A marcação anterior adiciona várias `using` instruções.</span><span class="sxs-lookup"><span data-stu-id="7a089-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="7a089-442">Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="7a089-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="7a089-443">Ocultar links de usuários que não têm permissão para alterar dados não protege o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7a089-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="7a089-444">Ocultar links torna o aplicativo mais amigável exibindo apenas links válidos.</span><span class="sxs-lookup"><span data-stu-id="7a089-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="7a089-445">Os usuários podem invadir as URLs geradas para invocar operações de edição e exclusão nos dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="7a089-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="7a089-446">A Razor página ou o controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-446">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="7a089-447">Atualizar detalhes</span><span class="sxs-lookup"><span data-stu-id="7a089-447">Update Details</span></span>

<span data-ttu-id="7a089-448">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="7a089-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="7a089-449">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="7a089-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="7a089-450">Adicionar ou remover um usuário de uma função</span><span class="sxs-lookup"><span data-stu-id="7a089-450">Add or remove a user to a role</span></span>

<span data-ttu-id="7a089-451">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="7a089-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="7a089-452">Removendo privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="7a089-452">Removing privileges from a user.</span></span> <span data-ttu-id="7a089-453">Por exemplo, ativar mudo de um usuário em um aplicativo de chat.</span><span class="sxs-lookup"><span data-stu-id="7a089-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="7a089-454">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="7a089-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="7a089-455">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="7a089-455">Test the completed app</span></span>

<span data-ttu-id="7a089-456">Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="7a089-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="7a089-457">Escolha uma senha forte: Use oito ou mais caracteres e, pelo menos, um caractere, número e símbolo em letras maiúsculas.</span><span class="sxs-lookup"><span data-stu-id="7a089-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="7a089-458">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="7a089-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="7a089-459">Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="7a089-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="7a089-460">Remover e atualizar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="7a089-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="7a089-461">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="7a089-462">Uma maneira fácil de testar o aplicativo concluído é iniciar três navegadores diferentes (ou sessões Incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="7a089-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="7a089-463">Em um navegador, registre um novo usuário (por exemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="7a089-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="7a089-464">Entre em cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="7a089-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="7a089-465">Verifique as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="7a089-465">Verify the following operations:</span></span>

* <span data-ttu-id="7a089-466">Os usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="7a089-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="7a089-467">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="7a089-468">Os gerentes podem aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="7a089-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="7a089-469">A `Details` exibição mostra os botões **aprovar** e **rejeitar** .</span><span class="sxs-lookup"><span data-stu-id="7a089-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="7a089-470">Os administradores podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="7a089-471">Usuário</span><span class="sxs-lookup"><span data-stu-id="7a089-471">User</span></span>                | <span data-ttu-id="7a089-472">Propagado pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="7a089-472">Seeded by the app</span></span> | <span data-ttu-id="7a089-473">Opções</span><span class="sxs-lookup"><span data-stu-id="7a089-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="7a089-474">No</span><span class="sxs-lookup"><span data-stu-id="7a089-474">No</span></span>                | <span data-ttu-id="7a089-475">Edite/exclua os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="7a089-476">Yes</span><span class="sxs-lookup"><span data-stu-id="7a089-476">Yes</span></span>               | <span data-ttu-id="7a089-477">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="7a089-478">Yes</span><span class="sxs-lookup"><span data-stu-id="7a089-478">Yes</span></span>               | <span data-ttu-id="7a089-479">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="7a089-480">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="7a089-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="7a089-481">Copie a URL para excluir e editar do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="7a089-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="7a089-482">Cole esses links no navegador do usuário de teste para verificar se o usuário de teste não pode executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="7a089-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="7a089-483">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="7a089-483">Create the starter app</span></span>

* <span data-ttu-id="7a089-484">Criar um Razor aplicativo de páginas chamado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="7a089-484">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="7a089-485">Crie o aplicativo com **contas de usuário individuais** .</span><span class="sxs-lookup"><span data-stu-id="7a089-485">Create the app with **Individual User Accounts** .</span></span>
  * <span data-ttu-id="7a089-486">Nomeie-o como "ContactManager" para que o namespace corresponda ao namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="7a089-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="7a089-487">`-uld` Especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="7a089-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="7a089-488">Adicionar *modelos/Contact. cs* :</span><span class="sxs-lookup"><span data-stu-id="7a089-488">Add *Models/Contact.cs* :</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="7a089-489">Scaffold o `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="7a089-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="7a089-490">Crie uma migração inicial e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="7a089-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="7a089-491">Atualize a âncora **ContactManager** no arquivo *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="7a089-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="7a089-492">Testar o aplicativo Criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="7a089-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="7a089-493">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="7a089-493">Seed the database</span></span>

<span data-ttu-id="7a089-494">Adicione a classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) à pasta de *dados* .</span><span class="sxs-lookup"><span data-stu-id="7a089-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="7a089-495">Chamada `SeedData.Initialize` de `Main` :</span><span class="sxs-lookup"><span data-stu-id="7a089-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="7a089-496">Teste se o aplicativo propagau o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7a089-496">Test that the app seeded the database.</span></span> <span data-ttu-id="7a089-497">Se houver linhas no BD de contato, o método de semente não será executado.</span><span class="sxs-lookup"><span data-stu-id="7a089-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="7a089-498">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7a089-498">Additional resources</span></span>

* [<span data-ttu-id="7a089-499">Criar um aplicativo Web .NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="7a089-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="7a089-500">[ASP.NET Core laboratório de autorização](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="7a089-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="7a089-501">Este laboratório apresenta mais detalhes sobre os recursos de segurança apresentados neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="7a089-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="7a089-502">Autorização baseada em política personalizada</span><span class="sxs-lookup"><span data-stu-id="7a089-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
