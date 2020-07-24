---
title: Criar um aplicativo ASP.NET Core com dados de usuário protegidos por autorização
author: rick-anderson
description: 'Saiba como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização. Inclui HTTPS, autenticação, segurança ASP.NET Core :::no-loc(Identity)::: .'
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/secure-data
ms.openlocfilehash: 7d4c10fa0b1c569179fc3e0a518917ec0185c51f
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160276"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="6a26f-104">Criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização</span><span class="sxs-lookup"><span data-stu-id="6a26f-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="6a26f-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="6a26f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="6a26f-106">Consulte [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="6a26f-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6a26f-107">Este tutorial mostra como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="6a26f-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="6a26f-108">Ele exibe uma lista de contatos que usuários autenticados (registrados) criaram.</span><span class="sxs-lookup"><span data-stu-id="6a26f-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="6a26f-109">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="6a26f-109">There are three security groups:</span></span>

* <span data-ttu-id="6a26f-110">**Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="6a26f-111">**Os gerentes** podem aprovar ou rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="6a26f-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="6a26f-112">Somente contatos aprovados são visíveis para os usuários.</span><span class="sxs-lookup"><span data-stu-id="6a26f-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="6a26f-113">**Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="6a26f-114">As imagens neste documento não correspondem exatamente aos modelos mais recentes.</span><span class="sxs-lookup"><span data-stu-id="6a26f-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="6a26f-115">Na imagem a seguir, o usuário Rick ( `rick@example.com` ) está conectado.</span><span class="sxs-lookup"><span data-stu-id="6a26f-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="6a26f-116">Rick só pode exibir contatos aprovados e **Editar** / **excluir** / **criar novos** links para seus contatos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="6a26f-117">Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** .</span><span class="sxs-lookup"><span data-stu-id="6a26f-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="6a26f-118">Outros usuários não verão o último registro até que um gerente ou Administrador altere o status para "aprovado".</span><span class="sxs-lookup"><span data-stu-id="6a26f-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="6a26f-120">Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:</span><span class="sxs-lookup"><span data-stu-id="6a26f-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de tela mostrando a manager@contoso.com entrada](secure-data/_static/manager1.png)

<span data-ttu-id="6a26f-122">A imagem a seguir mostra a exibição de detalhes dos gerentes de um contato:</span><span class="sxs-lookup"><span data-stu-id="6a26f-122">The following image shows the managers details view of a contact:</span></span>

![Exibição do gerente de um contato](secure-data/_static/manager.png)

<span data-ttu-id="6a26f-124">Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.</span><span class="sxs-lookup"><span data-stu-id="6a26f-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="6a26f-125">Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:</span><span class="sxs-lookup"><span data-stu-id="6a26f-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de tela mostrando a admin@contoso.com entrada](secure-data/_static/admin.png)

<span data-ttu-id="6a26f-127">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="6a26f-127">The administrator has all privileges.</span></span> <span data-ttu-id="6a26f-128">Ela pode ler/editar/excluir qualquer contato e alterar o status dos contatos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="6a26f-129">O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="6a26f-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="6a26f-130">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="6a26f-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="6a26f-131">`ContactIsOwnerAuthorizationHandler`: Garante que um usuário só possa editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="6a26f-132">`ContactManagerAuthorizationHandler`: Permite que os gerentes aprovem ou rejeitem contatos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="6a26f-133">`ContactAdministratorsAuthorizationHandler`: Permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6a26f-134">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="6a26f-134">Prerequisites</span></span>

<span data-ttu-id="6a26f-135">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="6a26f-135">This tutorial is advanced.</span></span> <span data-ttu-id="6a26f-136">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="6a26f-136">You should be familiar with:</span></span>

* [<span data-ttu-id="6a26f-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a26f-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="6a26f-138">Autenticação</span><span class="sxs-lookup"><span data-stu-id="6a26f-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="6a26f-139">Confirmação de conta e recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="6a26f-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="6a26f-140">Autorização</span><span class="sxs-lookup"><span data-stu-id="6a26f-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="6a26f-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6a26f-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="6a26f-142">O aplicativo iniciador e concluído</span><span class="sxs-lookup"><span data-stu-id="6a26f-142">The starter and completed app</span></span>

<span data-ttu-id="6a26f-143">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="6a26f-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="6a26f-144">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="6a26f-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="6a26f-145">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="6a26f-145">The starter app</span></span>

<span data-ttu-id="6a26f-146">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="6a26f-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="6a26f-147">Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="6a26f-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="6a26f-148">Proteger dados do usuário</span><span class="sxs-lookup"><span data-stu-id="6a26f-148">Secure user data</span></span>

<span data-ttu-id="6a26f-149">As seções a seguir têm todas as principais etapas para criar o aplicativo de dados de usuário seguro.</span><span class="sxs-lookup"><span data-stu-id="6a26f-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="6a26f-150">Talvez você ache útil fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="6a26f-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="6a26f-151">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="6a26f-151">Tie the contact data to the user</span></span>

<span data-ttu-id="6a26f-152">Use a [:::no-loc(Identity):::](xref:security/authentication/identity) ID de usuário ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="6a26f-152">Use the ASP.NET [:::no-loc(Identity):::](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="6a26f-153">Adicione `OwnerID` e `ContactStatus` ao `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="6a26f-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="6a26f-154">`OwnerID`é a ID do usuário da `AspNetUser` tabela no banco de [:::no-loc(Identity):::](xref:security/authentication/identity) dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [:::no-loc(Identity):::](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="6a26f-155">O `Status` campo determina se um contato é visível por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="6a26f-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="6a26f-156">Crie uma nova migração e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="6a26f-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="6a26f-157">Adicionar serviços de função a:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="6a26f-157">Add Role services to :::no-loc(Identity):::</span></span>

<span data-ttu-id="6a26f-158">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="6a26f-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="6a26f-159">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="6a26f-159">Require authenticated users</span></span>

<span data-ttu-id="6a26f-160">Defina a política de autenticação de fallback para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="6a26f-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="6a26f-161">O código realçado anterior define a [política de autenticação de fallback](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="6a26f-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="6a26f-162">A política de autenticação de fallback requer que ***todos*** os usuários sejam autenticados, com exceção de :::no-loc(Razor)::: páginas, controladores ou métodos de ação com um atributo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="6a26f-162">The fallback authentication policy requires ***all*** users to be authenticated, except for :::no-loc(Razor)::: Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="6a26f-163">Por exemplo, :::no-loc(Razor)::: páginas, controladores ou métodos de ação com `[AllowAnonymous]` ou `[Authorize(PolicyName="MyPolicy")]` usam o atributo de autenticação aplicado em vez da política de autenticação de fallback.</span><span class="sxs-lookup"><span data-stu-id="6a26f-163">For example, :::no-loc(Razor)::: Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="6a26f-164">A política de autenticação de fallback:</span><span class="sxs-lookup"><span data-stu-id="6a26f-164">The fallback authentication policy:</span></span>

* <span data-ttu-id="6a26f-165">É aplicado a todas as solicitações que não especificam explicitamente uma política de autenticação.</span><span class="sxs-lookup"><span data-stu-id="6a26f-165">Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="6a26f-166">Para solicitações servidas pelo roteamento de ponto de extremidade, isso inclui qualquer ponto de extremidade que não especifica um atributo de autorização.</span><span class="sxs-lookup"><span data-stu-id="6a26f-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="6a26f-167">Para solicitações servidas por outro middleware após o middleware de autorização, como [arquivos estáticos](xref:fundamentals/static-files), isso aplicaria a política a todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="6a26f-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="6a26f-168">Definir a política de autenticação de fallback para exigir que os usuários sejam autenticados protege :::no-loc(Razor)::: páginas e controladores adicionados recentemente.</span><span class="sxs-lookup"><span data-stu-id="6a26f-168">Setting the fallback authentication policy to require users to be authenticated protects newly added :::no-loc(Razor)::: Pages and controllers.</span></span> <span data-ttu-id="6a26f-169">Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e :::no-loc(Razor)::: páginas para incluir o `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="6a26f-169">Having authentication required by default is more secure than relying on new controllers and :::no-loc(Razor)::: Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="6a26f-170">A <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> classe também contém <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="6a26f-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="6a26f-171">O `DefaultPolicy` é a política usada com o `[Authorize]` atributo quando nenhuma política é especificada.</span><span class="sxs-lookup"><span data-stu-id="6a26f-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="6a26f-172">`[Authorize]`Não contém uma política nomeada, ao contrário de `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="6a26f-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="6a26f-173">Para obter mais informações sobre políticas, consulte <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="6a26f-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="6a26f-174">Uma maneira alternativa para os controladores e :::no-loc(Razor)::: páginas do MVC exigir que todos os usuários sejam autenticados é adicionar um filtro de autorização:</span><span class="sxs-lookup"><span data-stu-id="6a26f-174">An alternative way for MVC controllers and :::no-loc(Razor)::: Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="6a26f-175">O código anterior usa um filtro de autorização, a definição da política de fallback usa o roteamento de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="6a26f-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="6a26f-176">Definir a política de fallback é a maneira preferida de exigir que todos os usuários sejam autenticados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="6a26f-177">Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às `Index` páginas e `Privacy` para que os usuários anônimos possam obter informações sobre o site antes de se registrarem:</span><span class="sxs-lookup"><span data-stu-id="6a26f-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="6a26f-178">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="6a26f-178">Configure the test account</span></span>

<span data-ttu-id="6a26f-179">A `SeedData` classe cria duas contas: administrador e Gerenciador.</span><span class="sxs-lookup"><span data-stu-id="6a26f-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="6a26f-180">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="6a26f-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="6a26f-181">Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="6a26f-181">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="6a26f-182">Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.</span><span class="sxs-lookup"><span data-stu-id="6a26f-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="6a26f-183">Atualize `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="6a26f-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="6a26f-184">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="6a26f-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="6a26f-185">Atualize o `Initialize` método na `SeedData` classe para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="6a26f-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="6a26f-186">Adicione a ID de usuário do administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="6a26f-187">Faça um dos contatos "enviado" e um "rejeitado".</span><span class="sxs-lookup"><span data-stu-id="6a26f-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="6a26f-188">Adicione a ID de usuário e o status a todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="6a26f-189">Apenas um contato é mostrado:</span><span class="sxs-lookup"><span data-stu-id="6a26f-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="6a26f-190">Criar manipuladores de autorização de administrador, gerente e proprietário</span><span class="sxs-lookup"><span data-stu-id="6a26f-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="6a26f-191">Crie uma `ContactIsOwnerAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="6a26f-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6a26f-192">O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="6a26f-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="6a26f-193">O `ContactIsOwnerAuthorizationHandler` contexto de chamadas [. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="6a26f-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="6a26f-194">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="6a26f-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="6a26f-195">Retornar `context.Succeed` quando os requisitos forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="6a26f-196">Retornar `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="6a26f-197">`Task.CompletedTask`Não é êxito ou falha &mdash; permite que outros manipuladores de autorização sejam executados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="6a26f-198">Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="6a26f-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="6a26f-199">O aplicativo permite aos proprietários de contato editar/Excluir/criar seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="6a26f-200">`ContactIsOwnerAuthorizationHandler`Não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="6a26f-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="6a26f-201">Criar um manipulador de autorização de gerente</span><span class="sxs-lookup"><span data-stu-id="6a26f-201">Create a manager authorization handler</span></span>

<span data-ttu-id="6a26f-202">Crie uma `ContactManagerAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="6a26f-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6a26f-203">O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="6a26f-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="6a26f-204">Somente os gerentes podem aprovar ou rejeitar alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="6a26f-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="6a26f-205">Criar um manipulador de autorização de administrador</span><span class="sxs-lookup"><span data-stu-id="6a26f-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="6a26f-206">Crie uma `ContactAdministratorsAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="6a26f-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6a26f-207">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="6a26f-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="6a26f-208">O administrador pode realizar todas as operações.</span><span class="sxs-lookup"><span data-stu-id="6a26f-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="6a26f-209">Registrar os manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="6a26f-209">Register the authorization handlers</span></span>

<span data-ttu-id="6a26f-210">Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="6a26f-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="6a26f-211">O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity) , que se baseia em Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6a26f-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="6a26f-212">Registre os manipuladores com a coleção de serviços para que fiquem disponíveis para o `ContactsController` através de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6a26f-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6a26f-213">Adicione o seguinte código ao final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6a26f-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="6a26f-214">`ContactAdministratorsAuthorizationHandler`e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="6a26f-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="6a26f-215">Eles são singletons porque não usam o EF e todas as informações necessárias estão no `Context` parâmetro do `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="6a26f-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="6a26f-216">Autorização de suporte</span><span class="sxs-lookup"><span data-stu-id="6a26f-216">Support authorization</span></span>

<span data-ttu-id="6a26f-217">Nesta seção, você atualizará as :::no-loc(Razor)::: páginas e adicionará uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="6a26f-217">In this section, you update the :::no-loc(Razor)::: Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="6a26f-218">Examinar a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="6a26f-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="6a26f-219">Examine a `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="6a26f-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="6a26f-220">Essa classe contém os requisitos aos quais o aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="6a26f-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="6a26f-221">Criar uma classe base para as páginas de contatos :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="6a26f-221">Create a base class for the Contacts :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="6a26f-222">Crie uma classe base que contenha os serviços usados nas páginas de contatos :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="6a26f-222">Create a base class that contains the services used in the contacts :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="6a26f-223">A classe base coloca o código de inicialização em um local:</span><span class="sxs-lookup"><span data-stu-id="6a26f-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="6a26f-224">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="6a26f-224">The preceding code:</span></span>

* <span data-ttu-id="6a26f-225">Adiciona o `IAuthorizationService` serviço para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="6a26f-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="6a26f-226">Adiciona o :::no-loc(Identity)::: `UserManager` serviço.</span><span class="sxs-lookup"><span data-stu-id="6a26f-226">Adds the :::no-loc(Identity)::: `UserManager` service.</span></span>
* <span data-ttu-id="6a26f-227">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="6a26f-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="6a26f-228">Atualizar o CREATEMODEL</span><span class="sxs-lookup"><span data-stu-id="6a26f-228">Update the CreateModel</span></span>

<span data-ttu-id="6a26f-229">Atualize o Construtor criar modelo de página para usar a `DI_BasePageModel` classe base:</span><span class="sxs-lookup"><span data-stu-id="6a26f-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="6a26f-230">Atualize o `CreateModel.OnPostAsync` método para:</span><span class="sxs-lookup"><span data-stu-id="6a26f-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="6a26f-231">Adicione a ID de usuário ao `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="6a26f-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="6a26f-232">Chame o manipulador de autorização para verificar se o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="6a26f-233">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="6a26f-233">Update the IndexModel</span></span>

<span data-ttu-id="6a26f-234">Atualize o `OnGetAsync` método para que somente os contatos aprovados sejam mostrados para usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="6a26f-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="6a26f-235">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="6a26f-235">Update the EditModel</span></span>

<span data-ttu-id="6a26f-236">Adicione um manipulador de autorização para verificar se o usuário possui o contato.</span><span class="sxs-lookup"><span data-stu-id="6a26f-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="6a26f-237">Como a autorização de recursos está sendo validada, o `[Authorize]` atributo não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="6a26f-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="6a26f-238">O aplicativo não tem acesso ao recurso quando os atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="6a26f-239">A autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="6a26f-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="6a26f-240">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, seja carregando-o no modelo de página ou carregando-o dentro do próprio manipulador.</span><span class="sxs-lookup"><span data-stu-id="6a26f-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="6a26f-241">Você acessa com frequência o recurso passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="6a26f-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="6a26f-242">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="6a26f-242">Update the DeleteModel</span></span>

<span data-ttu-id="6a26f-243">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se o usuário tem a permissão Excluir no contato.</span><span class="sxs-lookup"><span data-stu-id="6a26f-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="6a26f-244">Injetar o serviço de autorização nas exibições</span><span class="sxs-lookup"><span data-stu-id="6a26f-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="6a26f-245">Atualmente, a interface do usuário mostra links de edição e exclusão de contatos que o usuário não pode modificar.</span><span class="sxs-lookup"><span data-stu-id="6a26f-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="6a26f-246">Insira o serviço de autorização no arquivo *pages/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="6a26f-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="6a26f-247">A marcação anterior adiciona várias `using` instruções.</span><span class="sxs-lookup"><span data-stu-id="6a26f-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="6a26f-248">Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="6a26f-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="6a26f-249">Ocultar links de usuários que não têm permissão para alterar dados não protege o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6a26f-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="6a26f-250">Ocultar links torna o aplicativo mais amigável exibindo apenas links válidos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="6a26f-251">Os usuários podem invadir as URLs geradas para invocar operações de edição e exclusão nos dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="6a26f-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="6a26f-252">A :::no-loc(Razor)::: página ou o controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-252">The :::no-loc(Razor)::: Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="6a26f-253">Atualizar detalhes</span><span class="sxs-lookup"><span data-stu-id="6a26f-253">Update Details</span></span>

<span data-ttu-id="6a26f-254">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="6a26f-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="6a26f-255">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="6a26f-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="6a26f-256">Adicionar ou remover um usuário de uma função</span><span class="sxs-lookup"><span data-stu-id="6a26f-256">Add or remove a user to a role</span></span>

<span data-ttu-id="6a26f-257">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="6a26f-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="6a26f-258">Removendo privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="6a26f-258">Removing privileges from a user.</span></span> <span data-ttu-id="6a26f-259">Por exemplo, ativar mudo de um usuário em um aplicativo de chat.</span><span class="sxs-lookup"><span data-stu-id="6a26f-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="6a26f-260">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="6a26f-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="6a26f-261">Diferenças entre desafio e proíba</span><span class="sxs-lookup"><span data-stu-id="6a26f-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="6a26f-262">Esse aplicativo define a política padrão para [exigir usuários autenticados](#rau).</span><span class="sxs-lookup"><span data-stu-id="6a26f-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="6a26f-263">O código a seguir permite usuários anônimos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-263">The following code allows anonymous users.</span></span> <span data-ttu-id="6a26f-264">Os usuários anônimos têm permissão para mostrar as diferenças entre o desafio versus proíba.</span><span class="sxs-lookup"><span data-stu-id="6a26f-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="6a26f-265">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="6a26f-265">In the preceding code:</span></span>

* <span data-ttu-id="6a26f-266">Quando o usuário **não** é autenticado, um `ChallengeResult` é retornado.</span><span class="sxs-lookup"><span data-stu-id="6a26f-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="6a26f-267">Quando um `ChallengeResult` é retornado, o usuário é redirecionado para a página de entrada.</span><span class="sxs-lookup"><span data-stu-id="6a26f-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="6a26f-268">Quando o usuário é autenticado, mas não autorizado, um `ForbidResult` é retornado.</span><span class="sxs-lookup"><span data-stu-id="6a26f-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="6a26f-269">Quando um `ForbidResult` é retornado, o usuário é redirecionado para a página acesso negado.</span><span class="sxs-lookup"><span data-stu-id="6a26f-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="6a26f-270">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="6a26f-270">Test the completed app</span></span>

<span data-ttu-id="6a26f-271">Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="6a26f-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="6a26f-272">Escolha uma senha forte: Use oito ou mais caracteres e, pelo menos, um caractere, número e símbolo em letras maiúsculas.</span><span class="sxs-lookup"><span data-stu-id="6a26f-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="6a26f-273">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="6a26f-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="6a26f-274">Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="6a26f-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="6a26f-275">Se o aplicativo tiver contatos:</span><span class="sxs-lookup"><span data-stu-id="6a26f-275">If the app has contacts:</span></span>

* <span data-ttu-id="6a26f-276">Exclua todos os registros na `Contact` tabela.</span><span class="sxs-lookup"><span data-stu-id="6a26f-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="6a26f-277">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="6a26f-278">Uma maneira fácil de testar o aplicativo concluído é iniciar três navegadores diferentes (ou sessões Incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="6a26f-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="6a26f-279">Em um navegador, registre um novo usuário (por exemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="6a26f-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="6a26f-280">Entre em cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="6a26f-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="6a26f-281">Verifique as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="6a26f-281">Verify the following operations:</span></span>

* <span data-ttu-id="6a26f-282">Os usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="6a26f-283">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="6a26f-284">Os gerentes podem aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="6a26f-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="6a26f-285">A `Details` exibição mostra os botões **aprovar** e **rejeitar** .</span><span class="sxs-lookup"><span data-stu-id="6a26f-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="6a26f-286">Os administradores podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="6a26f-287">Usuário</span><span class="sxs-lookup"><span data-stu-id="6a26f-287">User</span></span>                | <span data-ttu-id="6a26f-288">Propagado pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="6a26f-288">Seeded by the app</span></span> | <span data-ttu-id="6a26f-289">Opções</span><span class="sxs-lookup"><span data-stu-id="6a26f-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="6a26f-290">No</span><span class="sxs-lookup"><span data-stu-id="6a26f-290">No</span></span>                | <span data-ttu-id="6a26f-291">Edite/exclua os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="6a26f-292">Yes</span><span class="sxs-lookup"><span data-stu-id="6a26f-292">Yes</span></span>               | <span data-ttu-id="6a26f-293">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="6a26f-294">Yes</span><span class="sxs-lookup"><span data-stu-id="6a26f-294">Yes</span></span>               | <span data-ttu-id="6a26f-295">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="6a26f-296">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="6a26f-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="6a26f-297">Copie a URL para excluir e editar do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="6a26f-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="6a26f-298">Cole esses links no navegador do usuário de teste para verificar se o usuário de teste não pode executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="6a26f-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="6a26f-299">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="6a26f-299">Create the starter app</span></span>

* <span data-ttu-id="6a26f-300">Criar um :::no-loc(Razor)::: aplicativo de páginas chamado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="6a26f-300">Create a :::no-loc(Razor)::: Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="6a26f-301">Crie o aplicativo com **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="6a26f-301">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="6a26f-302">Nomeie-o como "ContactManager" para que o namespace corresponda ao namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="6a26f-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="6a26f-303">`-uld`Especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="6a26f-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="6a26f-304">Adicionar *modelos/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="6a26f-304">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="6a26f-305">Scaffold o `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="6a26f-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="6a26f-306">Crie uma migração inicial e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="6a26f-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="6a26f-307">Se você tiver um bug com o `dotnet aspnet-codegenerator razorpage` comando, consulte [este problema do GitHub](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="6a26f-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="6a26f-308">Atualize a âncora **ContactManager** no arquivo *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="6a26f-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="6a26f-309">Testar o aplicativo Criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="6a26f-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="6a26f-310">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="6a26f-310">Seed the database</span></span>

<span data-ttu-id="6a26f-311">Adicione a classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) à pasta de *dados* :</span><span class="sxs-lookup"><span data-stu-id="6a26f-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="6a26f-312">Chamada `SeedData.Initialize` de `Main` :</span><span class="sxs-lookup"><span data-stu-id="6a26f-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="6a26f-313">Teste se o aplicativo propagau o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-313">Test that the app seeded the database.</span></span> <span data-ttu-id="6a26f-314">Se houver linhas no BD de contato, o método de semente não será executado.</span><span class="sxs-lookup"><span data-stu-id="6a26f-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="6a26f-315">Este tutorial mostra como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização.</span><span class="sxs-lookup"><span data-stu-id="6a26f-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="6a26f-316">Ele exibe uma lista de contatos que usuários autenticados (registrados) criaram.</span><span class="sxs-lookup"><span data-stu-id="6a26f-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="6a26f-317">Há três grupos de segurança:</span><span class="sxs-lookup"><span data-stu-id="6a26f-317">There are three security groups:</span></span>

* <span data-ttu-id="6a26f-318">**Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="6a26f-319">**Os gerentes** podem aprovar ou rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="6a26f-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="6a26f-320">Somente contatos aprovados são visíveis para os usuários.</span><span class="sxs-lookup"><span data-stu-id="6a26f-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="6a26f-321">**Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="6a26f-322">Na imagem a seguir, o usuário Rick ( `rick@example.com` ) está conectado.</span><span class="sxs-lookup"><span data-stu-id="6a26f-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="6a26f-323">Rick só pode exibir contatos aprovados e **Editar** / **excluir** / **criar novos** links para seus contatos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="6a26f-324">Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** .</span><span class="sxs-lookup"><span data-stu-id="6a26f-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="6a26f-325">Outros usuários não verão o último registro até que um gerente ou Administrador altere o status para "aprovado".</span><span class="sxs-lookup"><span data-stu-id="6a26f-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

<span data-ttu-id="6a26f-327">Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:</span><span class="sxs-lookup"><span data-stu-id="6a26f-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Captura de tela mostrando a manager@contoso.com entrada](secure-data/_static/manager1.png)

<span data-ttu-id="6a26f-329">A imagem a seguir mostra a exibição de detalhes dos gerentes de um contato:</span><span class="sxs-lookup"><span data-stu-id="6a26f-329">The following image shows the managers details view of a contact:</span></span>

![Exibição do gerente de um contato](secure-data/_static/manager.png)

<span data-ttu-id="6a26f-331">Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.</span><span class="sxs-lookup"><span data-stu-id="6a26f-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="6a26f-332">Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:</span><span class="sxs-lookup"><span data-stu-id="6a26f-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Captura de tela mostrando a admin@contoso.com entrada](secure-data/_static/admin.png)

<span data-ttu-id="6a26f-334">O administrador tem todos os privilégios.</span><span class="sxs-lookup"><span data-stu-id="6a26f-334">The administrator has all privileges.</span></span> <span data-ttu-id="6a26f-335">Ela pode ler/editar/excluir qualquer contato e alterar o status dos contatos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="6a26f-336">O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="6a26f-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="6a26f-337">O exemplo contém os seguintes manipuladores de autorização:</span><span class="sxs-lookup"><span data-stu-id="6a26f-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="6a26f-338">`ContactIsOwnerAuthorizationHandler`: Garante que um usuário só possa editar seus dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="6a26f-339">`ContactManagerAuthorizationHandler`: Permite que os gerentes aprovem ou rejeitem contatos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="6a26f-340">`ContactAdministratorsAuthorizationHandler`: Permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6a26f-341">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="6a26f-341">Prerequisites</span></span>

<span data-ttu-id="6a26f-342">Este tutorial é avançado.</span><span class="sxs-lookup"><span data-stu-id="6a26f-342">This tutorial is advanced.</span></span> <span data-ttu-id="6a26f-343">Você deve estar familiarizado com:</span><span class="sxs-lookup"><span data-stu-id="6a26f-343">You should be familiar with:</span></span>

* [<span data-ttu-id="6a26f-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a26f-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="6a26f-345">Autenticação</span><span class="sxs-lookup"><span data-stu-id="6a26f-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="6a26f-346">Confirmação de conta e recuperação de senha</span><span class="sxs-lookup"><span data-stu-id="6a26f-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="6a26f-347">Autorização</span><span class="sxs-lookup"><span data-stu-id="6a26f-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="6a26f-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="6a26f-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="6a26f-349">O aplicativo iniciador e concluído</span><span class="sxs-lookup"><span data-stu-id="6a26f-349">The starter and completed app</span></span>

<span data-ttu-id="6a26f-350">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) .</span><span class="sxs-lookup"><span data-stu-id="6a26f-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="6a26f-351">[Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.</span><span class="sxs-lookup"><span data-stu-id="6a26f-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="6a26f-352">O aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="6a26f-352">The starter app</span></span>

<span data-ttu-id="6a26f-353">[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .</span><span class="sxs-lookup"><span data-stu-id="6a26f-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="6a26f-354">Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.</span><span class="sxs-lookup"><span data-stu-id="6a26f-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="6a26f-355">Proteger dados do usuário</span><span class="sxs-lookup"><span data-stu-id="6a26f-355">Secure user data</span></span>

<span data-ttu-id="6a26f-356">As seções a seguir têm todas as principais etapas para criar o aplicativo de dados de usuário seguro.</span><span class="sxs-lookup"><span data-stu-id="6a26f-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="6a26f-357">Talvez você ache útil fazer referência ao projeto concluído.</span><span class="sxs-lookup"><span data-stu-id="6a26f-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="6a26f-358">Vincular os dados de contato ao usuário</span><span class="sxs-lookup"><span data-stu-id="6a26f-358">Tie the contact data to the user</span></span>

<span data-ttu-id="6a26f-359">Use a [:::no-loc(Identity):::](xref:security/authentication/identity) ID de usuário ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários.</span><span class="sxs-lookup"><span data-stu-id="6a26f-359">Use the ASP.NET [:::no-loc(Identity):::](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="6a26f-360">Adicione `OwnerID` e `ContactStatus` ao `Contact` modelo:</span><span class="sxs-lookup"><span data-stu-id="6a26f-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="6a26f-361">`OwnerID`é a ID do usuário da `AspNetUser` tabela no banco de [:::no-loc(Identity):::](xref:security/authentication/identity) dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [:::no-loc(Identity):::](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="6a26f-362">O `Status` campo determina se um contato é visível por usuários gerais.</span><span class="sxs-lookup"><span data-stu-id="6a26f-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="6a26f-363">Crie uma nova migração e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="6a26f-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="6a26f-364">Adicionar serviços de função a:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="6a26f-364">Add Role services to :::no-loc(Identity):::</span></span>

<span data-ttu-id="6a26f-365">Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) para adicionar serviços de função:</span><span class="sxs-lookup"><span data-stu-id="6a26f-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="6a26f-366">Exigir usuários autenticados</span><span class="sxs-lookup"><span data-stu-id="6a26f-366">Require authenticated users</span></span>

<span data-ttu-id="6a26f-367">Defina a política de autenticação padrão para exigir que os usuários sejam autenticados:</span><span class="sxs-lookup"><span data-stu-id="6a26f-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="6a26f-368">Você pode recusar a autenticação na :::no-loc(Razor)::: página, controlador ou nível de método de ação com o `[AllowAnonymous]` atributo.</span><span class="sxs-lookup"><span data-stu-id="6a26f-368">You can opt out of authentication at the :::no-loc(Razor)::: Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="6a26f-369">Definir a política de autenticação padrão para exigir que os usuários sejam autenticados protege :::no-loc(Razor)::: páginas e controladores adicionados recentemente.</span><span class="sxs-lookup"><span data-stu-id="6a26f-369">Setting the default authentication policy to require users to be authenticated protects newly added :::no-loc(Razor)::: Pages and controllers.</span></span> <span data-ttu-id="6a26f-370">Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e :::no-loc(Razor)::: páginas para incluir o `[Authorize]` atributo.</span><span class="sxs-lookup"><span data-stu-id="6a26f-370">Having authentication required by default is more secure than relying on new controllers and :::no-loc(Razor)::: Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="6a26f-371">Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às páginas de índice, sobre e de contato para que os usuários anônimos possam obter informações sobre o site antes de se registrarem.</span><span class="sxs-lookup"><span data-stu-id="6a26f-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="6a26f-372">Configurar a conta de teste</span><span class="sxs-lookup"><span data-stu-id="6a26f-372">Configure the test account</span></span>

<span data-ttu-id="6a26f-373">A `SeedData` classe cria duas contas: administrador e Gerenciador.</span><span class="sxs-lookup"><span data-stu-id="6a26f-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="6a26f-374">Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas.</span><span class="sxs-lookup"><span data-stu-id="6a26f-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="6a26f-375">Defina a senha do diretório do projeto (o diretório que contém *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="6a26f-375">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="6a26f-376">Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.</span><span class="sxs-lookup"><span data-stu-id="6a26f-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="6a26f-377">Atualize `Main` para usar a senha de teste:</span><span class="sxs-lookup"><span data-stu-id="6a26f-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="6a26f-378">Criar as contas de teste e atualizar os contatos</span><span class="sxs-lookup"><span data-stu-id="6a26f-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="6a26f-379">Atualize o `Initialize` método na `SeedData` classe para criar as contas de teste:</span><span class="sxs-lookup"><span data-stu-id="6a26f-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="6a26f-380">Adicione a ID de usuário do administrador e `ContactStatus` aos contatos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="6a26f-381">Faça um dos contatos "enviado" e um "rejeitado".</span><span class="sxs-lookup"><span data-stu-id="6a26f-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="6a26f-382">Adicione a ID de usuário e o status a todos os contatos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="6a26f-383">Apenas um contato é mostrado:</span><span class="sxs-lookup"><span data-stu-id="6a26f-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="6a26f-384">Criar manipuladores de autorização de administrador, gerente e proprietário</span><span class="sxs-lookup"><span data-stu-id="6a26f-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="6a26f-385">Crie uma pasta de *autorização* e crie uma `ContactIsOwnerAuthorizationHandler` classe nela.</span><span class="sxs-lookup"><span data-stu-id="6a26f-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="6a26f-386">O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.</span><span class="sxs-lookup"><span data-stu-id="6a26f-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="6a26f-387">O `ContactIsOwnerAuthorizationHandler` contexto de chamadas [. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato.</span><span class="sxs-lookup"><span data-stu-id="6a26f-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="6a26f-388">Manipuladores de autorização geralmente:</span><span class="sxs-lookup"><span data-stu-id="6a26f-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="6a26f-389">Retornar `context.Succeed` quando os requisitos forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="6a26f-390">Retornar `Task.CompletedTask` quando os requisitos não forem atendidos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="6a26f-391">`Task.CompletedTask`Não é êxito ou falha &mdash; permite que outros manipuladores de autorização sejam executados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="6a26f-392">Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="6a26f-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="6a26f-393">O aplicativo permite aos proprietários de contato editar/Excluir/criar seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="6a26f-394">`ContactIsOwnerAuthorizationHandler`Não precisa verificar a operação passada no parâmetro de requisito.</span><span class="sxs-lookup"><span data-stu-id="6a26f-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="6a26f-395">Criar um manipulador de autorização de gerente</span><span class="sxs-lookup"><span data-stu-id="6a26f-395">Create a manager authorization handler</span></span>

<span data-ttu-id="6a26f-396">Crie uma `ContactManagerAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="6a26f-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6a26f-397">O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente.</span><span class="sxs-lookup"><span data-stu-id="6a26f-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="6a26f-398">Somente os gerentes podem aprovar ou rejeitar alterações de conteúdo (novas ou alteradas).</span><span class="sxs-lookup"><span data-stu-id="6a26f-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="6a26f-399">Criar um manipulador de autorização de administrador</span><span class="sxs-lookup"><span data-stu-id="6a26f-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="6a26f-400">Crie uma `ContactAdministratorsAuthorizationHandler` classe na pasta *Authorization* .</span><span class="sxs-lookup"><span data-stu-id="6a26f-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="6a26f-401">O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador.</span><span class="sxs-lookup"><span data-stu-id="6a26f-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="6a26f-402">O administrador pode realizar todas as operações.</span><span class="sxs-lookup"><span data-stu-id="6a26f-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="6a26f-403">Registrar os manipuladores de autorização</span><span class="sxs-lookup"><span data-stu-id="6a26f-403">Register the authorization handlers</span></span>

<span data-ttu-id="6a26f-404">Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="6a26f-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="6a26f-405">O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity) , que se baseia em Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="6a26f-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="6a26f-406">Registre os manipuladores com a coleção de serviços para que fiquem disponíveis para o `ContactsController` através de [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6a26f-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6a26f-407">Adicione o seguinte código ao final de `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6a26f-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="6a26f-408">`ContactAdministratorsAuthorizationHandler`e `ContactManagerAuthorizationHandler` são adicionados como singletons.</span><span class="sxs-lookup"><span data-stu-id="6a26f-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="6a26f-409">Eles são singletons porque não usam o EF e todas as informações necessárias estão no `Context` parâmetro do `HandleRequirementAsync` método.</span><span class="sxs-lookup"><span data-stu-id="6a26f-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="6a26f-410">Autorização de suporte</span><span class="sxs-lookup"><span data-stu-id="6a26f-410">Support authorization</span></span>

<span data-ttu-id="6a26f-411">Nesta seção, você atualizará as :::no-loc(Razor)::: páginas e adicionará uma classe de requisitos de operações.</span><span class="sxs-lookup"><span data-stu-id="6a26f-411">In this section, you update the :::no-loc(Razor)::: Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="6a26f-412">Examinar a classe de requisitos de operações de contato</span><span class="sxs-lookup"><span data-stu-id="6a26f-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="6a26f-413">Examine a `ContactOperations` classe.</span><span class="sxs-lookup"><span data-stu-id="6a26f-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="6a26f-414">Essa classe contém os requisitos aos quais o aplicativo dá suporte:</span><span class="sxs-lookup"><span data-stu-id="6a26f-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="6a26f-415">Criar uma classe base para as páginas de contatos :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="6a26f-415">Create a base class for the Contacts :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="6a26f-416">Crie uma classe base que contenha os serviços usados nas páginas de contatos :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="6a26f-416">Create a base class that contains the services used in the contacts :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="6a26f-417">A classe base coloca o código de inicialização em um local:</span><span class="sxs-lookup"><span data-stu-id="6a26f-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="6a26f-418">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="6a26f-418">The preceding code:</span></span>

* <span data-ttu-id="6a26f-419">Adiciona o `IAuthorizationService` serviço para acessar os manipuladores de autorização.</span><span class="sxs-lookup"><span data-stu-id="6a26f-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="6a26f-420">Adiciona o :::no-loc(Identity)::: `UserManager` serviço.</span><span class="sxs-lookup"><span data-stu-id="6a26f-420">Adds the :::no-loc(Identity)::: `UserManager` service.</span></span>
* <span data-ttu-id="6a26f-421">Adicione a `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="6a26f-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="6a26f-422">Atualizar o CREATEMODEL</span><span class="sxs-lookup"><span data-stu-id="6a26f-422">Update the CreateModel</span></span>

<span data-ttu-id="6a26f-423">Atualize o Construtor criar modelo de página para usar a `DI_BasePageModel` classe base:</span><span class="sxs-lookup"><span data-stu-id="6a26f-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="6a26f-424">Atualize o `CreateModel.OnPostAsync` método para:</span><span class="sxs-lookup"><span data-stu-id="6a26f-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="6a26f-425">Adicione a ID de usuário ao `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="6a26f-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="6a26f-426">Chame o manipulador de autorização para verificar se o usuário tem permissão para criar contatos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="6a26f-427">Atualizar o IndexModel</span><span class="sxs-lookup"><span data-stu-id="6a26f-427">Update the IndexModel</span></span>

<span data-ttu-id="6a26f-428">Atualize o `OnGetAsync` método para que somente os contatos aprovados sejam mostrados para usuários gerais:</span><span class="sxs-lookup"><span data-stu-id="6a26f-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="6a26f-429">Atualizar o EditModel</span><span class="sxs-lookup"><span data-stu-id="6a26f-429">Update the EditModel</span></span>

<span data-ttu-id="6a26f-430">Adicione um manipulador de autorização para verificar se o usuário possui o contato.</span><span class="sxs-lookup"><span data-stu-id="6a26f-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="6a26f-431">Como a autorização de recursos está sendo validada, o `[Authorize]` atributo não é suficiente.</span><span class="sxs-lookup"><span data-stu-id="6a26f-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="6a26f-432">O aplicativo não tem acesso ao recurso quando os atributos são avaliados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="6a26f-433">A autorização baseada em recursos deve ser imperativa.</span><span class="sxs-lookup"><span data-stu-id="6a26f-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="6a26f-434">As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, seja carregando-o no modelo de página ou carregando-o dentro do próprio manipulador.</span><span class="sxs-lookup"><span data-stu-id="6a26f-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="6a26f-435">Você acessa com frequência o recurso passando a chave de recurso.</span><span class="sxs-lookup"><span data-stu-id="6a26f-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="6a26f-436">Atualizar o DeleteModel</span><span class="sxs-lookup"><span data-stu-id="6a26f-436">Update the DeleteModel</span></span>

<span data-ttu-id="6a26f-437">Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se o usuário tem a permissão Excluir no contato.</span><span class="sxs-lookup"><span data-stu-id="6a26f-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="6a26f-438">Injetar o serviço de autorização nas exibições</span><span class="sxs-lookup"><span data-stu-id="6a26f-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="6a26f-439">Atualmente, a interface do usuário mostra links de edição e exclusão de contatos que o usuário não pode modificar.</span><span class="sxs-lookup"><span data-stu-id="6a26f-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="6a26f-440">Insira o serviço de autorização no arquivo *views/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:</span><span class="sxs-lookup"><span data-stu-id="6a26f-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="6a26f-441">A marcação anterior adiciona várias `using` instruções.</span><span class="sxs-lookup"><span data-stu-id="6a26f-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="6a26f-442">Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:</span><span class="sxs-lookup"><span data-stu-id="6a26f-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="6a26f-443">Ocultar links de usuários que não têm permissão para alterar dados não protege o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6a26f-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="6a26f-444">Ocultar links torna o aplicativo mais amigável exibindo apenas links válidos.</span><span class="sxs-lookup"><span data-stu-id="6a26f-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="6a26f-445">Os usuários podem invadir as URLs geradas para invocar operações de edição e exclusão nos dados que não possuem.</span><span class="sxs-lookup"><span data-stu-id="6a26f-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="6a26f-446">A :::no-loc(Razor)::: página ou o controlador deve impor verificações de acesso para proteger os dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-446">The :::no-loc(Razor)::: Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="6a26f-447">Atualizar detalhes</span><span class="sxs-lookup"><span data-stu-id="6a26f-447">Update Details</span></span>

<span data-ttu-id="6a26f-448">Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:</span><span class="sxs-lookup"><span data-stu-id="6a26f-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="6a26f-449">Atualize o modelo de página de detalhes:</span><span class="sxs-lookup"><span data-stu-id="6a26f-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="6a26f-450">Adicionar ou remover um usuário de uma função</span><span class="sxs-lookup"><span data-stu-id="6a26f-450">Add or remove a user to a role</span></span>

<span data-ttu-id="6a26f-451">Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obter informações sobre:</span><span class="sxs-lookup"><span data-stu-id="6a26f-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="6a26f-452">Removendo privilégios de um usuário.</span><span class="sxs-lookup"><span data-stu-id="6a26f-452">Removing privileges from a user.</span></span> <span data-ttu-id="6a26f-453">Por exemplo, ativar mudo de um usuário em um aplicativo de chat.</span><span class="sxs-lookup"><span data-stu-id="6a26f-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="6a26f-454">Adicionando privilégios a um usuário.</span><span class="sxs-lookup"><span data-stu-id="6a26f-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="6a26f-455">Testar o aplicativo concluído</span><span class="sxs-lookup"><span data-stu-id="6a26f-455">Test the completed app</span></span>

<span data-ttu-id="6a26f-456">Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:</span><span class="sxs-lookup"><span data-stu-id="6a26f-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="6a26f-457">Escolha uma senha forte: Use oito ou mais caracteres e, pelo menos, um caractere, número e símbolo em letras maiúsculas.</span><span class="sxs-lookup"><span data-stu-id="6a26f-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="6a26f-458">Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.</span><span class="sxs-lookup"><span data-stu-id="6a26f-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="6a26f-459">Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:</span><span class="sxs-lookup"><span data-stu-id="6a26f-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="6a26f-460">Remover e atualizar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="6a26f-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="6a26f-461">Reinicie o aplicativo para propagar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="6a26f-462">Uma maneira fácil de testar o aplicativo concluído é iniciar três navegadores diferentes (ou sessões Incognito/InPrivate).</span><span class="sxs-lookup"><span data-stu-id="6a26f-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="6a26f-463">Em um navegador, registre um novo usuário (por exemplo, `test@example.com` ).</span><span class="sxs-lookup"><span data-stu-id="6a26f-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="6a26f-464">Entre em cada navegador com um usuário diferente.</span><span class="sxs-lookup"><span data-stu-id="6a26f-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="6a26f-465">Verifique as seguintes operações:</span><span class="sxs-lookup"><span data-stu-id="6a26f-465">Verify the following operations:</span></span>

* <span data-ttu-id="6a26f-466">Os usuários registrados podem exibir todos os dados de contato aprovados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="6a26f-467">Os usuários registrados podem editar/excluir seus próprios dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="6a26f-468">Os gerentes podem aprovar/rejeitar dados de contato.</span><span class="sxs-lookup"><span data-stu-id="6a26f-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="6a26f-469">A `Details` exibição mostra os botões **aprovar** e **rejeitar** .</span><span class="sxs-lookup"><span data-stu-id="6a26f-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="6a26f-470">Os administradores podem aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="6a26f-471">Usuário</span><span class="sxs-lookup"><span data-stu-id="6a26f-471">User</span></span>                | <span data-ttu-id="6a26f-472">Propagado pelo aplicativo</span><span class="sxs-lookup"><span data-stu-id="6a26f-472">Seeded by the app</span></span> | <span data-ttu-id="6a26f-473">Opções</span><span class="sxs-lookup"><span data-stu-id="6a26f-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="6a26f-474">No</span><span class="sxs-lookup"><span data-stu-id="6a26f-474">No</span></span>                | <span data-ttu-id="6a26f-475">Edite/exclua os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="6a26f-476">Yes</span><span class="sxs-lookup"><span data-stu-id="6a26f-476">Yes</span></span>               | <span data-ttu-id="6a26f-477">Aprovar/rejeitar e editar/excluir os próprios dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="6a26f-478">Yes</span><span class="sxs-lookup"><span data-stu-id="6a26f-478">Yes</span></span>               | <span data-ttu-id="6a26f-479">Aprovar/rejeitar e editar/excluir todos os dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="6a26f-480">Crie um contato no navegador do administrador.</span><span class="sxs-lookup"><span data-stu-id="6a26f-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="6a26f-481">Copie a URL para excluir e editar do contato do administrador.</span><span class="sxs-lookup"><span data-stu-id="6a26f-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="6a26f-482">Cole esses links no navegador do usuário de teste para verificar se o usuário de teste não pode executar essas operações.</span><span class="sxs-lookup"><span data-stu-id="6a26f-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="6a26f-483">Criar o aplicativo inicial</span><span class="sxs-lookup"><span data-stu-id="6a26f-483">Create the starter app</span></span>

* <span data-ttu-id="6a26f-484">Criar um :::no-loc(Razor)::: aplicativo de páginas chamado "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="6a26f-484">Create a :::no-loc(Razor)::: Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="6a26f-485">Crie o aplicativo com **contas de usuário individuais**.</span><span class="sxs-lookup"><span data-stu-id="6a26f-485">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="6a26f-486">Nomeie-o como "ContactManager" para que o namespace corresponda ao namespace usado no exemplo.</span><span class="sxs-lookup"><span data-stu-id="6a26f-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="6a26f-487">`-uld`Especifica o LocalDB em vez do SQLite</span><span class="sxs-lookup"><span data-stu-id="6a26f-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="6a26f-488">Adicionar *modelos/Contact. cs*:</span><span class="sxs-lookup"><span data-stu-id="6a26f-488">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="6a26f-489">Scaffold o `Contact` modelo.</span><span class="sxs-lookup"><span data-stu-id="6a26f-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="6a26f-490">Crie uma migração inicial e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="6a26f-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="6a26f-491">Atualize a âncora **ContactManager** no arquivo *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="6a26f-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="6a26f-492">Testar o aplicativo Criando, editando e excluindo um contato</span><span class="sxs-lookup"><span data-stu-id="6a26f-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="6a26f-493">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="6a26f-493">Seed the database</span></span>

<span data-ttu-id="6a26f-494">Adicione a classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) à pasta de *dados* .</span><span class="sxs-lookup"><span data-stu-id="6a26f-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="6a26f-495">Chamada `SeedData.Initialize` de `Main` :</span><span class="sxs-lookup"><span data-stu-id="6a26f-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="6a26f-496">Teste se o aplicativo propagau o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6a26f-496">Test that the app seeded the database.</span></span> <span data-ttu-id="6a26f-497">Se houver linhas no BD de contato, o método de semente não será executado.</span><span class="sxs-lookup"><span data-stu-id="6a26f-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="6a26f-498">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6a26f-498">Additional resources</span></span>

* [<span data-ttu-id="6a26f-499">Criar um aplicativo Web .NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="6a26f-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="6a26f-500">[ASP.NET Core laboratório de autorização](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="6a26f-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="6a26f-501">Este laboratório apresenta mais detalhes sobre os recursos de segurança apresentados neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="6a26f-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="6a26f-502">Autorização baseada em política personalizada</span><span class="sxs-lookup"><span data-stu-id="6a26f-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
